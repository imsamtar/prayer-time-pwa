<script lang="ts">
  import { onMount } from "svelte";

  let lat = 0;
  let long = 0;

  let data = JSON.parse(localStorage.getItem("data"));

  onMount(fetchPrayerTime);

  async function fetchPrayerTime() {
    await new Promise((r) =>
      navigator.geolocation.getCurrentPosition((position) => {
        const { latitude, longitude } = position.coords;
        [lat, long] = [latitude, longitude];
        r(undefined);
      })
    );

    const date = new Date();
    const response = await fetch(
      `https://api.aladhan.com/v1/calendar?latitude=${lat}&longitude=${long}&method=10&month=${date.getMonth()}&year=${date.getFullYear()}`
    );
    const jsonResponse = await response.json();
    const { data: _data } = jsonResponse;
    data = _data.filter((day) => day.date.gregorian.day >= date.getDate());
    localStorage.setItem("data", JSON.stringify(data));
  }

  function filterTime(timeString: string) {
    return timeString.slice(0, timeString.indexOf("("));
  }
</script>

<main>
  {#if !data}
    ...
  {:else}
    {#each data as day}
      <div class="day">
        <div class="date">{day.date.gregorian.date}</div>
        <div class="time">
          <h3>Imsak</h3>
          {filterTime(day.timings.Imsak)}
        </div>
        <div class="time prayer">
          <h3>Fajr</h3>
          {filterTime(day.timings.Fajr)}
        </div>
        <div class="time">
          <h3>Sunrise</h3>
          {filterTime(day.timings.Sunrise)}
        </div>
        <div class="time prayer">
          <h3>Dhuhr</h3>
          {filterTime(day.timings.Dhuhr)}
        </div>
        <div class="time prayer">
          <h3>Asr</h3>
          {filterTime(day.timings.Asr)}
        </div>
        <div class="time">
          <h3>Sunset</h3>
          {filterTime(day.timings.Sunset)}
        </div>
        <div class="time prayer">
          <h3>Maghrib</h3>
          {filterTime(day.timings.Maghrib)}
        </div>
        <div class="time prayer">
          <h3>Isha</h3>
          {filterTime(day.timings.Isha)}
        </div>
        <div class="time">
          <h3>Midnight</h3>
          {filterTime(day.timings.Midnight)}
        </div>
      </div>
    {/each}
  {/if}
</main>

<style>
  :root {
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
      Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
      margin: 0;
      padding: 0;
  }

  :root::-webkit-scrollbar {
    display: none;
  }

  :global(body) {
    padding: 0;
    margin: 0;
  }

  main {
    text-align: center;
    padding: 0;
    margin: 0 auto;
  }
  .day {
    border: 2px solid red;
    display: flex;
    justify-content: space-evenly;
    padding: 0.5rem;
    padding-top: 4rem;
    margin: 0.5rem;
    position: relative;
    flex-wrap: wrap;
    gap: 0.1rem;
    overflow: hidden;
  }
  .date {
    position: absolute;
    left: 50%;
    top: 0;
    padding: 0.5rem;
    transform: translateX(-50%);
    font-size: 1.2rem;
    width: 100%;
    border-bottom: 2px solid red;
  }
  .time {
    min-width: 100px;
    border: 2px solid red;
    padding-bottom: 1rem;
  }
  .time.prayer {
    background: #ff000099;
    color: white;
  }
</style>
