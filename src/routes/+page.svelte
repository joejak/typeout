<script>
	import Typeout from '$lib/Typeout.svelte';
	import { onMount } from 'svelte';

	let to;
	let pause = true;
	let speed = 50;
	let cof = false;
	let content = `ou're pretty close using scrollTop == scrollHeight.

scrollTop refers to the top of the scroll position, which will be scrollHeight - offsetHeight

Your if statement should look like so (don't forget to use triple equals):

if( obj.scrollTop === (obj.scrollHeight - obj.offsetHeight))
{
}
Edit: Corrected my answer, was completely wrong

Share
Improve this answer
Follow
edited Aug 12, 2015 at 20:57
SomeKittens's user avatar
SomeKittens
39.4k1919 gold badges115115 silver badges145145 bronze badges
answered May 18, 2009 at 3:26
James Davies's user avatar
James Davies
9,76966 gold badges3939 silver badges4444 bronze badges
6
That almost works, scrollHeight-offsetHeight isn't exatly the same value as scrollTop, but after trying your code, if I require if that difference is fewer than 5 pixels for it to be the bottom I get the behavior I want. – 
Bjorn
 CommentedMay 18, 2009 at 4:20
2
this is not exact. obj.borderWidth need to be considered – 
looping
 CommentedOct 22, 2013 at 3:39
2
i prefer this answer: stackoverflow.com/questions/5828275/… – 
Chris
 CommentedFeb 12, 2014 at 10:49
8
scrollTop can be non-integral, so this requires some tolerance (say, obj.scrollHeight - obj.offsetHeight - obj.scrollTop < 1) to work in all circumstances. See stackoverflow.com/questions/5828275/… – 
Chris Martin
 CommentedAug 29, 2015 at 6:13 
maybe it has something to do with the fixed elements on my layout or something else odd I'm doing but the only time this evaluates to true is when I scroll all the way to the top. This is because document.body.scrollHeight equals document.body.offsetHeight in my instance (Chrome) – 
Evan de la Cruz
 CommentedSep 29, 2016 at 18:20`;
	let typer;
</script>

<h1>Welcome to your library project</h1>
<p>Create your package using @sveltejs/package and preview/showcase your work with SvelteKit</p>
<p>Visit <a href="https://svelte.dev/docs/kit">svelte.dev/docs/kit</a> to read the documentation</p>

<button
	on:click={() => {
		typer.stop();
	}}>finish</button
>

<button
	on:click={() => {
		typer.stop(true); 
	}}>Cancel</button
>


{#if pause}
	<button
		on:click={() => {
			typer.play();
			pause = false;
		}}>Play</button
	>
{:else}
	<button
		on:click={() => {
			typer.pause();
			pause = true;
		}}>Pause</button
	>
{/if}
<label for="speed">Speed</label>
<input
	style="writing-mode: horizontal-rl"
	type="range"
	name="speed"
	id="speed"
	bind:value={speed}
	min="1"
	max="100"
/>
<input type="numeric" disabled max="100" min="1" bind:value={speed} />
<br />
<textarea type="text" name="" id="" bind:value={content}></textarea>

<div style="height: 200px; overflow-y: scroll;">
<Typeout
	bind:this={typer}
	bind:content
	bind:speed
    trailLength=10
	on:complete={() => {
		pause = true;
		to = false;
	}}
></Typeout>
</div>
