<style>
	main {
		position: relative;
		max-width: 56em;
		background-color: white;
		padding: 2em;
		margin: 0 auto;
		box-sizing: border-box;
	}

	header h1, header p {
		text-align: center;
		margin: 0 auto;
	}

	h1 {
		font-size: 2.8em;
		text-transform: uppercase;
		font-weight: 700;
		margin: 0 0 0.5em 0;
	}

	p {
		margin: 1em auto;
	}

	a:link, a:visited {
		text-decoration: none;
		color: black;
	}

	a:hover {
		text-decoration: underline;
		color: blue;
	}
</style>

<header>
	<h1><a href=".">Webistat</a></h1>
	<p><a href="about">Stupidly Simple Statistics</a></p>
</header>

<main>
	<slot></slot>
</main>
