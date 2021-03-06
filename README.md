[![Node CI](https://github.com/YogliB/svelte-fullcalendar/workflows/Node%20CI/badge.svg)](https://github.com/YogliB/svelte-fullcalendar/actions?query=workflow%3A%22Node+CI%22)
[![Known Vulnerabilities](https://snyk.io/test/github/YogliB/svelte-fullcalendar/badge.svg)](https://snyk.io/test/github/YogliB/svelte-fullcalendar)
[![install size](https://badgen.net/packagephobia/install/svelte-fullcalendar)](https://packagephobia.now.sh/result?p=svelte-fullcalendar)
[![npm package version](https://badgen.net/npm/v/svelte-fullcalendar)](https://npm.im/svelte-fullcalendar)
[![Contributor Covenant](https://img.shields.io/badge/Contributor%20Covenant-v1.4%20adopted-ff69b4.svg)](code-of-conduct.md)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](http://makeapullrequest.com)
[![Gitpod ready-to-code](https://img.shields.io/badge/Gitpod-ready--to--code-blue?logo=gitpod)](https://gitpod.io/#https://github.com/YogliB/svelte-fullcalendar)

# svelte-fullcalendar

## A Svelte 3 component-wrapper for [FullCalendar](https://fullcalendar.io)

FullCalendar seamlessly integrates with the [Svelte](https://svelte.dev) JavaScript compiler and the [Sapper](https://sapper.svelte.dev) JavaScript framework. It provides a component that exactly matches the functionality of FullCalendar's standard API.

This component is released under a MIT license, the same license the standard version of FullCalendar uses.

Useful links:

-   [Example project](https://github.com/YogliB/svelte-fullcalendar/tree/master/examples/svelte) (the code in this guide loosely follows it)
-   [Runnable project](https://svelte.dev/repl/afa33232d6914c5f9fd25e332e167a7c?version=3.12.1) in a code playground

This guide does not go into depth about initializing a Svelte project. Please consult the aforementioned example/runnable projects for that.

The first step is to install the FullCalendar-related dependencies. You'll need the Svelte adapter, the core package, and any additional plugins you plan to use:

```bash
npm install --save svelte-fullcalendar @fullcalendar/daygrid
```

You may then begin to write a parent component that leverages the `<FullCalendar>` component ([App.svelte](https://github.com/YogliB/svelte-fullcalendar/blob/master/examples/svelte/src/App.svelte)):

```html
<script>
	import FullCalendar from 'svelte-fullcalendar';
	import dayGridPlugin from '@fullcalendar/daygrid';

	let options = { initialView: 'dayGridMonth', plugins: [dayGridPlugin] };
</script>

<FullCalendar {options} />
```

You must initialized your calendar with at least one plugin that provides a view!

## CSS

All of FullCalendar’s CSS will be automatically loaded as long as your build system is able to process .css file imports. See [Initializing with an ES6 Build System](https://fullcalendar.io/docs/initialize-es6) for more information on configuring your build system.

## Props and Emitted Events

For the FullCalendar connector, there is no distinction between props and events. Everything is passed into the master `options` object as key-value pairs. Here is an example that demonstrates passing in an `events` array and a `dateClick` handler:

```html
<script>
	let options = {
		dateClick: (event) => alert('date click! ' + event.dateStr),
		events: [
			{ title: 'event 1', date: '2019-04-01' },
			{ title: 'event 2', date: '2019-04-02' },
		],
		initialView: dayGridMonth,
		plugins: [dayGridPlugin],
	};
</script>

<FullCalendar {options} />
```

## Modifying Options

You can modify your calendar’s options after initialization by reassigning them within the options object and reassign the `options` object. This is an example of changing the `weekends` options:

```html
<script>
	import FullCalendar from 'svelte-fullcalendar';
	import dayGridPlugin from '@fullcalendar/daygrid';

	let options = {
		initialView: dayGridMonth,
		plugins: [dayGridPlugin],
		weekends: false,
	};

	function toggleWeekends() {
		options.weekends = !options.weekends;
		options = { ...options };
	}
</script>

<button on:click="{toggleWeekends}">toggle weekends</button>
<FullCalendar {options} />
```

## FullCalendar Utilities

All of FullCalendar’s utility functions that would normally be accessible via `@fullcalendar/core` will also be accessible via `svelte-fullcalendar`. The formatDate utility for example. This prevents you from needing to add another dependency to your project.

```html
<script>
	import { formatDate } from 'svelte-fullcalendar';

	let str = formatDate(new Date(), {
		month: 'long',
		year: 'numeric',
		day: 'numeric',
	});

	console.log(str);
</script>
```

## Calendar API

Hopefully you won’t need to do it often, but sometimes it’s useful to access the underlying `Calendar` object for raw data and methods.

This is especially useful for controlling the current date. The [initialDate](https://fullcalendar.io/docs/initialDate) prop will set the initial date of the calendar, but to change it after that, you’ll need to rely on the [date navigation methods](https://fullcalendar.io/docs/date-navigation).

To do something like this, you’ll need to get ahold of the component’s ref (short for “reference”). In the template:

```html
<FullCalendar bind:this="{calendarRef}" {options} />
```

Once you have the ref, you can get the underlying Calendar object via the getApi method:

```html
<script>
	let calendarRef;

	function next() {
		let calendarApi = calendarRef.getAPI();
		calendarApi.next();
	}
</script>
```

## Scheduler

How do you use [FullCalendar Scheduler's](https://fullcalendar.io/docs/premium) premium plugins with Svelte? They are no different than any other plugin. Just follow the same instructions as you did `dayGridPlugin` in the above example. Also, make sure to include your `schedulerLicenseKey`:

```html
<script>
	import FullCalendar from 'svelte-fullcalendar';
	import resourceTimelinePlugin from '@fullcalendar/resource-timeline';

	let options = {
		plugins: [resourceTimelinePlugin],
		schedulerLicenseKey: 'XXX',
	};
</script>

<FullCalendar {options} />
```

Also, make sure all the correct stylesheets are being included.

## Draggable external events

You'll need to install the `interactionPlugin`:

```bash
npm install @fullcalendar/interaction
```

See the [official docs](https://fullcalendar.io/docs/external-dragging) for all available props.

Here is a simple usage example:

```html
<script>
	import FullCalendar, { Draggable } from 'svelte-fullcalendar';
	import resourceTimelinePlugin from '@fullcalendar/resource-timeline';
	import interactionPlugin from '@fullcalendar/interaction';
</script>

<Draggable eventData={{ title: 'my event', duration: '02:00' }}>
	Drag me!
</Draggable>

<FullCalendar
	schedulerLicenseKey="XXX"
	plugins="{[resourceTimelinePlugin, interactionPlugin]}"
	droppable="{true}"
/>
```
