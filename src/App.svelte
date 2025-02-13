<script lang="ts">
  // imports
  import "./app.css";
  import GameplayComponent from "./lib/GameplayComponent.svelte";
  import type { Matchup } from "./types";
  import Papa from "papaparse";

  // local variables
  let matchupList: Matchup[] = [];
  let maxRounds = 10;
  let gameStarted = false;

  // local functions

  // pasrses CSV file and places data into rows
  const parseCSV = (csvFile: string) => {
    return new Promise<Matchup[]>((resolve, reject) => {
      Papa.parse(csvFile, {
        header: true,
        dynamicTyping: true,
        complete: (results: { data: Matchup[] }) => {
          resolve(results.data as Matchup[]);
        },
        error: (error: any) => {
          reject(error);
        },
      });
    });
  };

  // get a random set of scores
  const getRandomScores = async () => {
    matchupList = [];
    const csvFile = await fetch("../scores.csv").then((res) => res.text());
    const data = await parseCSV(csvFile);
    const usedIndices = new Set<number>();
    while (matchupList.length < maxRounds) {
      const randomIndex = Math.floor(Math.random() * data.length);
      if (!usedIndices.has(randomIndex)) {
      usedIndices.add(randomIndex);
      matchupList.push(data[randomIndex]);
      }
    }
    gameStarted = true;
  };

  const endGame = () => {
    gameStarted = false;
    console.log("Game ended");
  };
</script>

<main class="flex flex-col text-center p-8 gap-16">
  <!-- header section -->
  <div class="header flex flex-col gap-4">
    <h1 class="text-3xl font-semibold">Guess the NFL Score</h1>
    <p class="text-xl">
      Simple game for guessing NFL scores across various season
    </p>
  </div>

  <!-- gameplay section -->
  <div class="gameplay flex flex-col gap-8 items-center">
    {#if gameStarted}
      <GameplayComponent {matchupList} {maxRounds} {endGame} />
    {:else}
      <h1 class="text-2xl animate-pulse">Waiting for game to start...</h1>
    {/if}
  </div>

  <!-- start button and options -->
  <div
    hidden={gameStarted}
    class="flex flex-col gap-4 w-1/3 m-auto justify-center items-center"
  >
    <button
      class="bg-green-500 px-8 py-2 text-white rounded w-1/2 text-2xl"
      onclick={getRandomScores}>Start</button
    >
    <div class="flex flex-row gap-4 justify-center items-center text-xl">
      <div class="flex flex-col gap-4 bg-gray-200 roudned p-2 text-center w-48">
        <label for="season">Season:</label>
        <select name="season" id="season" class="bg-white p-1 text-center">
          <option value="2022">2022</option>
          <option value="2023">2023</option>
          <option value="2024">2024</option>
        </select>
      </div>
      <div class="flex flex-col gap-4 bg-gray-200 roudned p-2 text-center w-48">
        <label for="max-rounds">Max Rounds:</label>
        <input
          type="number"
          name="max-rounds"
          id="max-rounds"
          class="bg-white p-1 text-center"
          defaultValue={10}
          onchange={(e) => {
            if (e.target) {
              maxRounds = parseInt((e.target as HTMLInputElement).value, 10);
            }
          }}
        />
      </div>
    </div>
  </div>
</main>
