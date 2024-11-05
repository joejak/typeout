<script>
	import { createEventDispatcher, onMount } from 'svelte';

	export let content = 'Hello There!';
	export let autoPlay = true;
	export let autoScroll = false;
	export let randomStutterFactor = 1;
	export let cursorChar = '&#x258C;'; //$'//'&#x25AF';
	export let trailLength = 10;
	export let delay = 0;
    export let speed = 50;

	export function pause() {
		paused = true;
	}

	export function play() {
		if (paused) {
			paused = false;
			play();
		}

		if (element.innerHTML != content) {
			if (element.innerHTML == '') {
				typeEffect();
				return;
			}
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
					typeEffect();
					break;
				}
			}
		} else {
			stop();
		}
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
		setTimeout(() => {
			trail.remove();
		}, breakMultiplier * 10);
		console.log('stop');
		clearInterval(timer);
		trailArr = [];
		dispatcher('complete');
	}

    export function clear(){
        element.innerHTML = ''; 
        cursorIndex = 0;
        dispatcher('cleared'); 
    }

	let trailArr = [];
	let paused = true;
	let cursorIndex = 0;
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
	let breakMultiplier = 0;
	$: realSpeed = Math.ceil((1 / speed) * 1.2 * 100);

	function isBottomedOut() {
		return (
			element.parentElement.scrollTop >
			element.parentElement.scrollHeight - element.parentElement.offsetHeight - 50
		);
	}

	onMount(() => {
		if (autoPlay) {
			play();
		} else if (delay) {
			setTimeout(() => {
				play();
			}, delay);
		}
	});

	let parentElementWidth = 0;
	let parentElementHeight = 0;
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
			parentElementHeight = element.parentElement.offsetHeight;
			trail.style += `padding-right:${parentElementWidth / 2}px; padding-bottom:${parentElementHeight / 2}px`;
		}
		timer = setInterval(() => {
			if (breakMultiplier > 0) {
				breakMultiplier--;
				return;
			} else if (autoScroll) {
				console.log('scrolling');
				trail.scrollIntoView({
					block: 'end',
					inline: 'end',
					behavior: 'instant'
				});
			}

			if (!element) {
				return;
			}

			if (!paused) {
				letter = content.charAt(cursorIndex++);

				switch (content.charAt(cursorIndex + 1)) {
					case '':
						breakMultiplier = 0;
						break;

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

				trailArr.pop();
				if (letter) trailArr.push(letter);
				trailArr.push(cursorChar);
			}

			breakMultiplier = breakMultiplier + Math.random() * randomStutterFactor;

			if (trailArr.length > 1) {
				if (trailLength < trailArr.length || letter == '' || (trailArr.length > 1 && paused)) {
					element.innerHTML += trailArr.shift();
				}

				trail.innerHTML = '';
				let calcDuration = (realSpeed + randomStutterFactor) * trailLength * 10;
				for (let t = 0; t < trailArr.length; t++) {
					const child = document.createElement('span');
					child.innerHTML = trailArr[t];
					child.style = `animation-name: shrink; animation-duration: ${calcDuration}ms; animation-delay:${0 - calcDuration * ((trailArr.length - (t + 1)) / trailArr.length)}ms;`;
					if (t == trailArr.length - 1) {
						child.style = `font-family: sans-serif; animation-name: shrink; animation-duration: ${calcDuration}ms; animation-delay:${0 - calcDuration * ((trailArr.length - (t + 1)) / trailArr.length)}ms; ${autoScroll?'padding-right: 12px':''}`;
					}
					trail.append(child);
				}
			} else if (paused) {
				return;
			} else {
				stop(false);
			}
		}, realSpeed);
		return timer;
	}
</script>

<pre style="display:inline" bind:this={element}><pre class="trail" bind:this={trail}> <span
			class="trail-segment"></span></pre></pre>

<style>
	pre {
		display: inline;
	}

	.trail {
		margin-left: 0px;
	}

	pre,
	span {
		font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial,
			sans-serif;
	}

	@keyframes -global-shrink {
		from {
			opacity: 0.75;
			color: lime;
		}
		to {
			color: black;
			opacity: 1;
		}
	}
</style>
