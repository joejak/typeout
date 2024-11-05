<script>
	import Typeout from '$lib/Typeout.svelte';

	let pause = true;
	let speed = 90;
	let randomStutterFactor = 5;
	let trailLength = 30;
	let content = `Type in your demo here!`;
	let cursorChar = '&#x258C;';
	let charPreview;
	let autoScroll = true;
	let typer;
</script>

<header style="display:flex; flex-direction:column; align-items:center;  margin-bottom: 2em;">
	<h2><Typeout speed="90" content="Typeout.<i style='color:orange'>sѵeʅte</i>"></Typeout></h2>
	<div style="">
		<Typeout
			autoPlay={false}
			delay="900"
			content="A handy, customizable component to create a cool typing effect! Try it out below!"
		></Typeout>
	</div>
</header>

<div style="margin-left: 2em; margin-right: 2em;">
	<div style="display:flex; flex-direction:row; gap: 2em;">
		<div style="display:flex; flex-direction: column;">
			<div style="display:flex; justify-content:space-between;">
				<label for="speed">Speed</label>
				<input style="width: 50px;" type="numeric" disabled max="100" min="1" bind:value={speed} />
			</div>

			<input
				style="writing-mode: horizontal-rl"
				type="range"
				name="speed"
				id="speed"
				bind:value={speed}
				min="1"
				max="100"
			/>

			<br />

			<div style="display:flex; justify-content:space-between; ">
				<label for="speed">Random Stutter Factor</label>
				<input
					style="width: 50px;"
					type="numeric"
					disabled
					max="100"
					min="1"
					bind:value={randomStutterFactor}
				/>
			</div>

			<input
				style="writing-mode: horizontal-rl"
				type="range"
				name="speed"
				id="speed"
				bind:value={randomStutterFactor}
				min="1"
				max="200"
			/>

			<br />

			<div style="display:flex; justify-content:space-between; ">
				<label for="speed">Cursor Trail Length</label>
				<input
					style="width: 50px;"
					type="numeric"
					disabled
					max="200"
					min="1"
					bind:value={trailLength}
				/>
			</div>

			<input
				style="writing-mode: horizontal-rl"
				type="range"
				name="speed"
				id="speed"
				bind:value={trailLength}
				min="1"
				max="250"
			/>

			<br />

			<div style="display:flex; flex-direction:column; justify-content:space-between; ">
				<label for="speed" style="margin-bottom: 6px;">Cursor Character</label>
				<div style="display:flex; justify-content:space-between">
					<input style="width: 120px;" type="text" bind:value={cursorChar} />
					<pre
						style="text-align: center; flex-grow:1; margin: 0px; padding: 0px; margin-left: 20px; margin-right: 20px; padding: 2px; background-color: grey; color: lime;"
						bind:this={charPreview}
						contenteditable
						bind:innerHTML={cursorChar}></pre>
				</div>
			</div>

			<br />

			<div
				style="display:flex; flex-direction:row; justify-content: space-around; gap: 4px; margin-bottom: 4px; "
			>
				<button
					style="flex-grow:1"
					on:click={() => {
						typer.stop();
					}}>Finish</button
				>

				<button
					style="flex-grow: 1"
					on:click={() => {
						typer.stop(true);
					}}>Cancel</button
				>
			</div>
			{#if pause}
				<button
					style="background-color: #b3ffb3;"
					on:click={() => {
						pause = false;
						typer.play();
					}}>Play</button
				>
			{:else}
				<button
					style="background-color: #f3f3af;"
					on:click={() => {
						pause = true;
						typer.pause();
					}}>Pause</button
				>
			{/if}
			<button
				style="margin-top: 4px;"
				on:click={() => {
					typer.content = '';
					typer.clear();
				}}>Clear</button
			>
		</div>
		<div style="display:flex; flex-direction:column; flex-grow:1">
			<textarea
				style="flex-grow: 1; border-radius: 12px; padding: 8px; "
				type="text"
				name=""
				id=""
				bind:value={content}
			></textarea>
		</div>
	</div>

	<br />
	<p style="margin: 0px; padding:0px; margin-left:auto; color:grey;">output</p>
	<div style="height: 200px; overflow-y: scroll; border: solid; border-radius: 12px; padding:8px;">
		<Typeout
			bind:this={typer}
			bind:content
			bind:speed
			bind:randomStutterFactor
			bind:trailLength
			autoScroll={true}
			autoPlay={false}
			on:complete={() => {
				console.log('complete');
				pause = true;
			}}
		></Typeout>
	</div>
</div>

<style>
	button {
		border-radius: 4px;
	}

	::-webkit-scrollbar {
		width: 12px;
	}

	::-webkit-scrollbar-track {
		box-shadow: inset 0 0 10px 10px rgb(212, 212, 212);
		border: solid 0px;
		border-top-right-radius: 10px;
		border-bottom-right-radius: 10px;
	}

	::-webkit-scrollbar-thumb {
		box-shadow: inset 0 0 10px 10px rgb(110, 117, 133);
		border: solid 0px;
		border-top-right-radius: 8px;
		border-bottom-right-radius: 8px;
	}

	::-webkit-scrollbar-track:horizontal {
		box-shadow: inset 0 0 10px 10px rgb(212, 212, 212);
		border: solid 0px;
		border-bottom-left-radius: 10px;
		border-bottom-right-radius: 10px;
		border-top-right-radius: 0px;
	}

	::-webkit-scrollbar-thumb:horizontal {
		box-shadow: inset 0 0 10px 10px rgb(110, 117, 133);
		border: solid 0px;
		border-top-right-radius: 0px;
		border-bottom-left-radius: 8px;
		border-bottom-right-radius: 8px;
	}

	textarea::-webkit-scrollbar-track {
		box-shadow: inset 0 0 10px 10px rgb(212, 212, 212);
		border: solid 0px;
		border-top-right-radius: 10px;
		border-bottom-right-radius: 0px;
	}

	textarea::-webkit-scrollbar-thumb {
		box-shadow: inset 0 0 8px 8px rgb(110, 117, 133);
		border: solid 0px;
		border-top-right-radius: 8px;
		border-bottom-right-radius: 0px;
	}

	label {
		margin-right: 1em;
	}
</style>
