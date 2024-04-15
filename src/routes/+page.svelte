<script>
    import { journalSentences } from "$lib/sentences.js";
    import { morse } from "$lib/morse.js";
    import { fade } from "svelte/transition";
    import { Howl } from "howler";

    const notes = ["A", "As", "B", "C", "Cs", "D", "Ds", "E", "F", "Fs", "G", "Gs"];

    let title = "morse";
    let sentence = generateRandomSentence();
    let convertedSentence = convertToMorseCode(sentence);
    let sentenceAnimation = [];
    let sentenceAnimationCounter = 0;
    let sentenceAnimationInterval = 20;
    let texture1;
    let texture2;
    let texture3;
    let texture4;
    let incidental1;
    let incidental2;
    let incidental3;
    let incidentalPlaying = false;
    let playing = false;
    let volume = 1;

    function generateRandomSentence() {
        let randomSentence = journalSentences[Math.floor(Math.random() * journalSentences.length)].toUpperCase();
        return randomSentence;
    }

    //converts the sentence to morse code
    function convertToMorseCode(string) {
        let morseString = [];
        //iterates through the string and checks if the character is a space, if it is, it skips it, if it isn't, it pushes an array for the morse code for the character to the morseString array
        for (let i = 0; i < string.length; i++) {
            if (morse[string[i]] !== undefined) {
                morseString.push(morse[string[i]].join(" "));
            }
        }
        //return morseString as a string
        return morseString.join("");
    }

    function playTexture() {
        texture1 = new Howl({
            src: ["/I-wish-you-could-decode-my-shorts-and-longs/texture1.mp3"],
            volume: volume,
            loop: true,
        });
        texture2 = new Howl({
            src: ["/I-wish-you-could-decode-my-shorts-and-longs/texture2.mp3"],
            volume: volume,
            loop: true,
        });
        texture3 = new Howl({
            src: ["/I-wish-you-could-decode-my-shorts-and-longs/texture3.mp3"],
            volume: volume,
            loop: true,
        });
        texture4 = new Howl({
            src: ["/I-wish-you-could-decode-my-shorts-and-longs/texture4.mp3"],
            volume: volume,
            loop: true,
        });
        texture1.play();
        setTimeout(() => {
            texture2.play();
        }, 4500);
        setTimeout(() => {
            texture3.play();
        }, 8000);
        setTimeout(() => {
            texture4.play();
        }, 12750);
    }

    function generateIncidentals() {
        let randomIncidental = Math.floor(Math.random() * 3) + 1;
        let chanceToPlayIncidental = Math.floor(Math.random() * 100) + 1;

        if (incidentalPlaying === false && chanceToPlayIncidental <= 20) {
            playIncidentals(randomIncidental);
            incidentalPlaying = true;
        }

        setTimeout(generateIncidentals, 1000);
    }

    function playIncidentals(number) {
        incidental1 = new Howl({
            src: ["/I-wish-you-could-decode-my-shorts-and-longs/incidental1.mp3"],
            volume: volume * 0.5,
            onend: function () {
                incidentalPlaying = false;
            },
        });
        incidental2 = new Howl({
            src: ["/I-wish-you-could-decode-my-shorts-and-longs/incidental2.mp3"],
            volume: volume * 0.5,
            onend: function () {
                incidentalPlaying = false;
            },
        });
        incidental3 = new Howl({
            src: ["/I-wish-you-could-decode-my-shorts-and-longs/incidental3.mp3"],
            volume: volume * 0.5,
            onend: function () {
                incidentalPlaying = false;
            },
        });

        if (number === 1) {
            incidental1.play();
        } else if (number === 2) {
            incidental2.play();
        } else if (number === 3) {
            incidental3.play();
        }
    }

    function makeRandomSound(length) {
        const randomNote = notes[Math.floor(Math.random() * notes.length)];
        let sound;
        if (length === "short") {
            sound = new Howl({
                src: [`/I-wish-you-could-decode-my-shorts-and-longs/${randomNote}.mp3`],
                volume: volume * 0.25,
            });
        } else if (length === "long") {
            sound = new Howl({
                src: [`/I-wish-you-could-decode-my-shorts-and-longs/${randomNote}-long.mp3`],
                volume: volume * 0.25,
            });
        }
        sound.play();
    }

    function animateSentence() {
        if (sentenceAnimationCounter <= convertedSentence.length) {
            let character = convertedSentence[sentenceAnimationCounter];
            if (character === ".") {
                sentenceAnimation.push("•");
                makeRandomSound("short");
                sentenceAnimationInterval = Math.floor(Math.random() * (120 - 90 + 1) + 90); //for performance
            } else if (character === "-") {
                sentenceAnimation.push("—");
                makeRandomSound("long");
                sentenceAnimationInterval = Math.floor(Math.random() * (350 - 250 + 1) + 250); //for performance
            } else if (character === " ") {
                sentenceAnimation.push(" ");
                sentenceAnimationInterval = Math.floor(Math.random() * (800 - 700 + 1) + 800); //for performance
            }
            sentenceAnimation = sentenceAnimation;
            sentenceAnimationCounter++;
            setTimeout(animateSentence, sentenceAnimationInterval);
        } else {
            sentence = generateRandomSentence();
            convertedSentence = convertToMorseCode(sentence);
            sentenceAnimation = [];
            sentenceAnimationCounter = 0;
            setTimeout(animateSentence, 4000);
        }
    }

    function begin() {
        playing = true;
        setTimeout(playTexture, 2000);
        setTimeout(generateIncidentals, 6000);
        setTimeout(animateSentence, 10000);
    }
</script>

<main class="bg-dark">
    <div class="container min-vh-100 d-flex align-items-center container">
        {#if !playing}
            <div class="w-100 d-flex flex-column align-items-center" transition:fade>
                <!-- svelte-ignore a11y-no-static-element-interactions -->
                <!-- svelte-ignore a11y-click-events-have-key-events -->
                <div class="titles" on:click={() => (title === "morse" ? (title = "text") : (title = "morse"))}>
                    {#if title === "morse"}
                        <h1 class="display-3 text-light text-center">•• •—— •• ••• •••• 🤞 —•—— ——— ••— —•—• ——— ••— •—•• —•• —•• • —•—• ——— —•• •— •••• • ••• • ••• •••• ——— •—• — ••• •— —• —•• •—•• ——— —• ——• •••</h1>
                    {:else}
                        <h1 class="display-3 text-light text-center">I wish 🤞 you could decode my shorts and longs</h1>
                    {/if}
                </div>
                <button on:click={begin} class="btn btn-outline-light border border-2 btn-lg mt-5">Play</button>
            </div>
        {:else if sentenceAnimation.length > 0}
            <h1 class="display-1 text-light" transition:fade>{sentenceAnimation.join("")}</h1>
        {/if}
    </div>
</main>

<style>
    @import url("https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,100;0,300;0,400;0,500;0,700;0,900;1,100;1,300;1,400;1,500;1,700;1,900&display=swap");

    main {
        background: linear-gradient(95deg, #111111, #181818, #202020);
        background-size: 500% 500%;
        animation: gradient-animation 10s ease infinite;
    }

    .container {
        opacity: 0;
        animation: fadeIn 1s forwards;
    }

    h1,
    button {
        font-family: "Roboto", sans-serif;
    }

    .titles {
        user-select: none;
    }

    @keyframes fadeIn {
        to {
            opacity: 1;
        }
    }

    @keyframes gradient-animation {
        0% {
            background-position: 0% 50%;
        }
        50% {
            background-position: 100% 50%;
        }
        100% {
            background-position: 0% 50%;
        }
    }
</style>
