<svelte:head>
	<title>About Webistat</title>
</svelte:head>

<h1>About Webistat</h1>

<p>
	This is a web port of Poul-Henning Kamp's
	<a href="https://www.freebsd.org/cgi/man.cgi?query=ministat">ministat</a>
	utility, as distributed in <a href="https://www.freebsd.org/">FreeBSD</a>,
	and commonly used for verifying performance improvements during development.
</p>

<p>
	It's intended for simple statistical analysis of numeric data, using
	<a href="https://en.wikipedia.org/wiki/Student%27s_t-test">Student's T</a>
	analysis to determine whether one result differs from another with a given
	degree of confidence.
</p>

<p>
	To use, simply feed it two sets of numbers, be they game FPS samples, benchmark
	runtimes, network latencies, or even temperature samples taken across two
	different days.
</p>

<p>
	The tool will give you some basic statistics for each list, and inform you
	whether they differ.  If <em>No difference proven</em> is reported, then you
	might need more samples, or there may be no meaningful difference between your
	results.
</p>
