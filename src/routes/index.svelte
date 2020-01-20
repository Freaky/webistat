<script>
	import throttle from 'lodash/throttle';
	import { stores } from '@sapper/app';
	const { page } = stores();

	const BaseURL = "/webistat/";

	import { Dataset } from "../webistat.js";

	const confidences = ["80", "90", "95", "98", "99", "99.5"];

	let confidence = $page.query['conf'] || "95";
	let baseline = $page.query['baseline'] || "";
	let comparison = $page.query['comparison'] || "";

	let result;

	function string_to_values(str) {
		let vals = str.replace(/#[^\n]*/g, '').match(/[-+]?[0-9]*\.?[0-9]+(?:[eE][-+]?[0-9]+)?/g);
		if (vals) {
			return vals.map(a => parseFloat(a, 10));
		} else {
			return false;
		}
	}

	function toQuery(vals) {
		return Object.keys(vals).map(key => key + '=' + encodeURIComponent(vals[key])).join('&');
	}

	function ministat(confidence, baseline, comparison) {
		let conf = confidences.findIndex(e => e == confidence);

		if (typeof(history) != 'undefined') {
			history.replaceState(null, "", BaseURL + "?" + toQuery({ conf: confidence, baseline: baseline, comparison: comparison }));
		}

		let av = string_to_values(baseline);
		let bv = string_to_values(comparison);

		if (!av || !bv) { return false; }

		let a = Dataset("Baseline", av);
		let b = Dataset("Comparison", bv);

		return {
			baseline: a.stats(),
			comparison: b.stats(),
			relative: b.relative(a, conf)
		};
	}

	let throttled_ministat = throttle(ministat, 100);

	$: result = throttled_ministat(confidence, baseline, comparison);
</script>

<style>


	label {
		display: block;
	}

	textarea {
		width: 100%;
		height: 200px;
	}

	table {
		width: 100%;
		border: 0;
		border-collapse: collapse;
	}

	th {
		border-bottom: 1px solid black;
	}

	th, td {
		text-align: right;
	}
</style>

<svelte:head>
	<title>Webistat</title>
</svelte:head>


<form>
	<fieldset>
		<legend>Free-form Numeric Data</legend>

		<label>Baseline<br>
			<textarea bind:value={baseline} placeholder="4, 8, 15, 16, 23 and 42"></textarea>
		</label>

		<label>Comparison<br>
			<textarea bind:value={comparison} placeholder="(Anything not a number is ignored)"></textarea>
		</label>

		<label>Confidence Level
			<select bind:value={confidence}>
				{#each confidences as conf}
					<option value={conf}>
						{conf}%
					</option>
				{/each}
			</select>
		</label>
	</fieldset>

	{#if result}
		<fieldset><legend>Results</legend>
			<table>
				<tr><th>N</th><th>Min</th><th>Max</th><th>Median</th><th>Avg</th><th>Stddev</th></tr>
				<tr>
					<td>{result.baseline.n}</td>
					<td>{result.baseline.min}</td>
					<td>{result.baseline.max}</td>
					<td>{result.baseline.median.toFixed(4)}</td>
					<td>{result.baseline.avg.toFixed(4)}</td>
					<td>{result.baseline.stddev.toFixed(4)}</td>
				</tr>
				<tr>
					<td>{result.comparison.n}</td>
					<td>{result.comparison.min}</td>
					<td>{result.comparison.max}</td>
					<td>{result.comparison.median.toFixed(4)}</td>
					<td>{result.comparison.avg.toFixed(4)}</td>
					<td>{result.comparison.stddev.toFixed(4)}</td>
				</tr>
			</table>
			{#if result.relative.proven}
				<p>Difference at <strong>{result.relative.confidence}%</strong> confidence</p>
				<p style="margin: 0 1em; padding: 0">
					<strong>{result.relative.diff.abs.toFixed(4)}</strong> ± <strong>{result.relative.diff.abs_err.toFixed(4)}</strong><br>
					<strong>{result.relative.diff.pct.toFixed(4)}%</strong> ± <strong>{result.relative.diff.pct_err.toFixed(4)}%</strong><br>
				(Student's t, pooled s = <strong>{result.relative.pooled_stddev.toFixed(4)})</strong></p>
			{:else}
				<p><strong>No difference proven</strong> at <strong>{result.relative.confidence}%</strong> confidence</p>
			{/if}
		</fieldset>
	{/if}
</form>
