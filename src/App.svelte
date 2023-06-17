<script>
  import DateInput from "./lib/DateInput.svelte";
  import { onMount } from "svelte";

  const WEEKS_IN_YEAR = 52;
  const LIFE_YEARS = 100;

  import { APP_NAME } from "./lib/name";

  let weeks = WEEKS_IN_YEAR * LIFE_YEARS;

  $: birthday = new Date(2001, 4, 28);
  let today = new Date();

  // Number of weeks since birthday
  $: week_age = Math.floor(
   (1 - 1/(52*7))*(today.getTime() - birthday.getTime()) / (1000 * 60 * 60 * 24 * 7)
  );
  $: console.log(week_age);
  $: birthweeks = [0];

  onMount(() => {
    // Write the constants to CSS variables
    document.documentElement.style.setProperty(
      "--weeks-in-year",
      WEEKS_IN_YEAR + ""
    );
    document.documentElement.style.setProperty("--life-years", LIFE_YEARS + "");

    // Now mark all by birthdays
    for (let i = 0; i < LIFE_YEARS; i++) {
      let bday = new Date(birthday);
      bday.setFullYear(bday.getFullYear() + i);
      birthweeks.push(
        Math.floor(
          (bday.getTime() - birthday.getTime()) / (1000 * 60 * 60 * 24 * 7)
        )
      );
    }
    console.log(birthweeks);
  });

  $: xyo = today.getFullYear() - birthday.getFullYear();
</script>

<main>
  <h1 style="font-size: min(calc(100dvw / {APP_NAME.length}), 2em);">
    {APP_NAME}
  </h1>
  <hr />
  <div id="core_layout">
    <div id="week_labels">
      {#each Array(WEEKS_IN_YEAR) as _, week}
        <span>
          {(week + 1) % 5 ? "" : week + 1}
        </span>
      {/each}
    </div>
    <div id="age_labels">
      {#each Array(LIFE_YEARS) as _, age}
        <div style="position: relative; width: 2em;">
          {#if ((age == xyo) || !(age % 5))}
            <div
              style="position: absolute; top:-5px; right:0px;"
              class='{age == xyo ? "ca" : ""}'
            >
              {age}
            </div>
          {/if}
        </div>
      {/each}
    </div>
    <div id="weeks_grid">
      {#each [...Array(weeks)] as i, week}
        <div
          class="week {week < week_age ? 'past' : ''} {week == week_age ? 'current' : ''}"
          on:keypress={() => {
            console.log(week);
          }}
          on:click={() => {
            console.log(week);
          }}
        />
      {/each}
    </div>
  </div>

  <div id="pgrnd" />
</main>

<section style="height: 100vh; display: grid; place-items:center;">
  <div style="text-align: center;">
    <h3>When do you celebrate your trip around the sun?</h3>
    <DateInput bind:date={birthday} />
  </div>
</section>

<style>
</style>
