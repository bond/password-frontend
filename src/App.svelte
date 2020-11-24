<script>

  export const name = "Password Generator App"

  const PASSWD_API = "https://europe-west1-ninja-hacks.cloudfunctions.net/passwordcf"

  const MIN_WORDS = 3
  const MAX_WORDS = 10

  let password
  let passwordArr = []
  let nWords = MIN_WORDS

  function ascii_special_chars() {
    // flatten array of ascii-chars
    return [[32, 64], [91, 96], [123,126]]
      .map(x => Array.from({length: x[1] - x[0] + 1}, (a, i) => i + x[0]))
      .flat()
  }

  Array.prototype.random = function () {
    return this[Math.floor((Math.random()*this.length))];
  }

  Array.prototype.passJoin = function(sep) {
    let chars = ascii_special_chars()
    return this.map(x => x + String.fromCharCode(chars.random()))
      .join('').slice(0, -1)
  }

  $: password = passwordArr.passJoin(" ")

  const getPassword = (event) => {
    if (parseInt(nWords) < MIN_WORDS) nWords = MIN_WORDS
    if (parseInt(nWords) > MAX_WORDS) nWords = MAX_WORDS

    fetch(`${PASSWD_API}?numWords=${nWords}`)
      .then(response => response.json())
      .then(data => passwordArr = data.password.split(' ') )
      .catch(function(error) {
        console.log(error);
      })
  }

</script>

<main>
  <section class="section">
    <div class="container">
      <h1 class="title">
        Passord generator
      </h1>
      <p class="subtitle">
        Genererer passord basert på tilfeldige <strong>Norske ord</strong>!
      </p>
      <form on:submit|preventDefault="{getPassword}">
        <div class="field">
          <label for="num_words" class="label">Antall ord ({MIN_WORDS} - {MAX_WORDS})</label>
          <div class="control">
            <input id="num_words" class="input" type="text" bind:value="{nWords}" autofocus>
          </div>
          <p class="help">Antall ord som ønskes i passord</p>
        </div>
        {#if password}
        <div class="field">
          <label for="passwd" class="label">Ditt passord</label>
          <div class="control">
            <input id="passwd" class="input" type="text" value="{password}" placeholder="Ditt passord dukker opp her">
          </div>
          <p class="help">Passordet kan tilpasses ved å redigere det</p>
        </div>
        {/if}
        <div class="control">
          <button class="button is-primary">Generer passord</button>
        </div>
      </form>
    </div>
  </section>
</main>

<style>
	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>