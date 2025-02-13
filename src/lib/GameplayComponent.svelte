<script lang="ts">
    // import
    import type { Matchup } from "../types";

    // props
    export let matchupList: Matchup[];
    export let maxRounds: number;
    export let endGame: () => void;

    // local variables
    let currentRound = 0;
    let awayGuess = 0;
    let homeGuess = 0;
    let correctGuesses = 0;
    let guessed = false;
    let correct = false;

    // local function
    const guessScore = () => {
        console.log(awayGuess);
        console.log(homeGuess);
        if (
            awayGuess === matchupList[currentRound].awayScore &&
            homeGuess === matchupList[currentRound].homeScore
        ) {
            correct = true;
            correctGuesses += 1;
        } else {
            correct = false;
        }
        guessed = true;
    };

    const setNextRound = () => {
        currentRound += 1;
        awayGuess = 0;
        homeGuess = 0;
        guessed = false;
        correct = false;
    };

    const finishGame = () => {
        endGame();
    };
</script>

<div class="flex flex-col gap-8">
    <h1 class="text-2xl">
        2025 Season | Week {matchupList[currentRound].week}
    </h1>
    <div
        class="px-8 py-4"
        style="background-color: {guessed
            ? correct
                ? '#bbf7d0'
                : '#fecaca'
            : '#e5e7eb'}"
    >
        {#if !guessed}
            <h1 class="text-3xl animate-pulse">Waiting for guess...</h1>
        {:else}
            <div class="text-3xl">
                {#if correct}
                    <h1>
                        Correct! ({matchupList[currentRound].awayScore} - {matchupList[
                            currentRound
                        ].homeScore})
                    </h1>
                {:else}
                    <h1>
                        Incorrect! ({matchupList[currentRound].awayScore} - {matchupList[
                            currentRound
                        ].homeScore})
                    </h1>
                {/if}
            </div>
        {/if}
    </div>
    <div class="flex flex-row gap-8 items-center">
        <div class="flex flex-col justify-center items-center gap-4">
            <div class="flex bg-gray-200 rounded-xl justify-center w-72">
                <img
                    src="./nfl_logos/{matchupList[
                        currentRound
                    ].awayTeam.toLocaleLowerCase()}.png"
                    alt={matchupList[currentRound].awayTeam}
                    class="w-40 h-40"
                />
            </div>
            <input
                type="number"
                name="away-score-guess"
                id="away-score-guess"
                bind:value={awayGuess}
                class="border-gray-200 border-4 rounded-xl w-32 h-12 text-center text-xl"
            />
        </div>
        <h1 class="items-center text-4xl font-bold">VS</h1>
        <div class="flex flex-col justify-center items-center gap-4">
            <div class="flex bg-gray-200 rounded-xl justify-center w-72">
                <img
                    src="./nfl_logos/{matchupList[
                        currentRound
                    ].homeTeam.toLocaleLowerCase()}.png"
                    alt={matchupList[currentRound].homeTeam}
                    class="w-40 h-40"
                />
            </div>
            <input
                type="number"
                name="home-score-guess"
                id="home-score-guess"
                bind:value={homeGuess}
                class="border-gray-200 border-4 rounded-xl w-32 h-12 text-center text-xl"
            />
        </div>
    </div>
    <div class="flex justify-center gap-8">
        <button
            disabled={guessed}
            class="disabled:bg-green-200 text-3xl w-32 flex bg-green-500 px-8 py-2 rounded-xl items-center justify-center text-white"
            onclick={guessScore}>Guess</button
        >
        {#if currentRound < maxRounds - 1}
            <button
                disabled={!guessed || currentRound === maxRounds - 1}
                class="disabled:bg-sky-200 text-3xl w-32 flex bg-sky-500 px-8 py-2 rounded-xl items-center justify-center text-white"
                onclick={setNextRound}>Next</button
            >
        {:else}
            <button
                disabled={!guessed}
                class="disabled:bg-amber-200 text-3xl w-32 flex bg-amber-500 px-8 py-2 rounded-xl items-center justify-center text-white"
                onclick={finishGame}>Finish</button
            >
        {/if}
    </div>
    <div>
        <h1 class="text-2xl font-semibold">
            Score: {correctGuesses}/{maxRounds}
        </h1>
    </div>
</div>
