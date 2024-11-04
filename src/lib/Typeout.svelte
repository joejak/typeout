<script>
	import { createEventDispatcher, onMount } from 'svelte';

	export let content = 'Hello There!';
	export let completeNow = false;
	export let delay = 0;
	export let autoScroll = true;
	export let cancel = false;
	export let randomStutterFactor = 2;
	export let cursorIndex = 0;
	export let cursorChar = '&#x25AF';
	export let trailLength = 3;

	let paused = true;

	export function pause() {
		paused = true;
	}

	export function play() {
		if (paused) {
			paused = false;
			play();
		}
		if (element.innerHTML != content) {
			for (
				let i = 0;
				i < (element.innerHTML.length > content.length ? element.innerHTML.length : content.length);
				i++
			) {
				if (element.innerHTML[i] != content[i]) {
					console.log('updating cursor to', i);
					cursorIndex = i;
					element.innerHTML = element.innerHTML.substring(0, i);
					trailArr = [];
					break;
				}
			}
		}
		typeEffect();
	}

	export function stop(cancel) {
		if (cancel) {
			trailArr.pop();
			for (let char of trailArr) {
				element.innerHTML += char;
			}
			content = element.innerHTML;
		} else {
			element.innerHTML = content;
			cursorIndex = content.length;
		}
		console.log('stop');
		clearInterval(timer);
		trailArr = [];
		trail.remove();
		dispatcher('complete');
	}

	let trailArr = [];

	let dispatcher = createEventDispatcher();

	/**
	 * @type {HTMLElement}
	 */
	let element;
	/**
	 * @type {HTMLElement}
	 */
	let trail;
	var timer;

	export let speed = 50;
	let breakMultiplier = 0;
	$: realSpeed = Math.ceil((1 / speed) * 1.2 * 100);

	function isBottomedOut() {
		return (
			element.parentElement.scrollTop >
			element.parentElement.scrollHeight - element.parentElement.offsetHeight - 50
		);
	}

	onMount(() => {});
	let typing;
	let mousedown = false;
	let parentElementWidth = 0;
	function typeEffect() {
		element.insertAdjacentElement('afterend', trail);
		clearInterval(timer);
		console.log('run');
		let letter = undefined;
		if (autoScroll) {
			element.parentElement.addEventListener('wheel', (ev) => {
				autoScroll = false;
			});
			element.parentElement.addEventListener('scrollend', () => {
				autoScroll = isBottomedOut();
			});
			element.parentElement.addEventListener('mousedown', () => {
				autoScroll = false;
			});
			element.parentElement.addEventListener('mouseup', () => {
				autoScroll = isBottomedOut();
			});
			parentElementWidth = element.parentElement.offsetWidth;
		}
		timer = setInterval(() => {
			if (breakMultiplier > 0) {
				breakMultiplier--;
				return;
			} else if (autoScroll) {
				console.log('scrolling');
				trail.scrollIntoView({
					block: 'end',
					inline: 'center',
					behavior: 'instant'
				});
			}

			if (!element || paused) {
				return;
			}

			letter = content.charAt(cursorIndex++);

			switch (content.charAt(cursorIndex + 1)) {
				// Uppercase letters
				case 'A':
				case 'B':
				case 'C':
				case 'D':
				case 'E':
				case 'F':
				case 'G':
				case 'H':
				case 'I':
				case 'J':
				case 'K':
				case 'L':
				case 'M':
				case 'N':
				case 'O':
				case 'P':
				case 'Q':
				case 'R':
				case 'S':
				case 'T':
				case 'U':
				case 'V':
				case 'W':
				case 'X':
				case 'Y':
				case 'Z':
					breakMultiplier = realSpeed * 1.5;
					break;
				// Lowercase letters
				case 'a':
				case 'b':
				case 'c':
				case 'd':
				case 'e':
				case 'f':
				case 'g':
				case 'h':
				case 'i':
				case 'j':
				case 'k':
				case 'l':
				case 'm':
				case 'n':
				case 'o':
				case 'p':
				case 'q':
				case 'r':
				case 's':
				case 't':
				case 'u':
				case 'v':
				case 'w':
				case 'x':
				case 'y':
				case 'z':
					breakMultiplier = realSpeed * 1.1;
					break;

				// Common punctuation
				case '.':
				case ',':
				case ';':
				case ':':
				case '!':
				case '?':
				case '-':
				case '_':
				case '(':
				case ')':
				case '{':
				case '}':
				case '[':
				case ']':
				case '"':
				case "'":
				case '/':
				case '\\':
				case '|':
				case '`':
				case '~':
				case '@':
				case '#':
				case '$':
				case '%':
				case '^':
				case '&':
				case '*':
				case '+':
				case '=':
					breakMultiplier = realSpeed * 2.5;
					break;

				// Default case for unhandled characters
				default:
					breakMultiplier = realSpeed * 3;
					break;
			}
			breakMultiplier = breakMultiplier + Math.random() * randomStutterFactor;
			trailArr.pop();
			if (letter) trailArr.push(letter);
			trailArr.push(cursorChar);

			//console.log(`${cursorIndex} | ${trailArr}`);
			if (trailArr.length > 1) {
				//console.log(trailLength < trailArr.length, letter);
				if (trailLength < trailArr.length || cursorIndex > content.length - trailLength) {
					element.innerHTML += trailArr.shift();
				}

				trail.innerHTML = '';
				for (let t = 0; t < trailArr.length; t++) {
					const child = document.createElement('span');
					child.innerHTML = trailArr[t];
					child.style = `animation-name: shrink; animation-duration: ${realSpeed * trailLength * 10}ms; animation-delay: ${realSpeed * trailLength * 10 * (t / trailLength) - realSpeed * trailLength * 10}ms`;
					console.log(child);
					trail.append(child);
				}
			} else {
				stop(false);
			}
		}, realSpeed);
		return timer;
	}
</script>

<pre style="display:inline" bind:this={element}></pre>
<pre class="trail" style="padding-right: {parentElementWidth / 5}px  " bind:this={trail}><span
		class="trail-segment"></span></pre>

<style>
	pre {
		display: inline;
		margin: 0px;
		padding: 0px;
	}

	.trail {
		margin-left: 0px;
	}

	.trail-segment {
		animation: 0.5s linear shrink;
	}

	@keyframes -global-shrink {
		from {
            opacity: .75;
			font-weight: 900;
			color: lime;
		}
		to {
			color: black;
			font-weight: normal;
            opacity: 1; 
		}
	}
</style>
