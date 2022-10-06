<script>
  import Clipboard from 'svelte-clipboard'

  let vars = {
    range: {
      value: 15,
      min: 1,
      max: 30,
    },
    lowercase: true,
    uppercase: false,
    numbers: false,
    symbols: false,
    duplicates: false,
    spaces: false,
    randomPassword: '',
    copy: false,
  }

  const characters = {
    lowercase: 'abcdefghijklmnopqrstuvwxyz',
    uppercase: 'ABCDEFGHIJKLMNOPQRSTUVWXYZ',
    numbers: '0123456789',
    symbols: '^!$%&|[](){}:;.,*+-#@<>~',
  }

  let percent
  $: percent = (vars.range.value / vars.range.max) * 100

  const generatePassword = () => {
    vars.randomPassword = ''
    let staticPassword = ''

    if (vars.lowercase) staticPassword += characters['lowercase']
    if (vars.symbols) staticPassword += characters['symbols']
    if (vars.uppercase) staticPassword += characters['uppercase']
    if (vars.numbers) staticPassword += characters['numbers']

    if (vars.spaces) staticPassword = `  ${staticPassword}  `

    for (let i = 0; i < vars.range.value; i++) {
      let randomChar =
        staticPassword[Math.floor(Math.random() * staticPassword.length)]
      if (vars.duplicates) {
        !vars.randomPassword.includes(randomChar) || randomChar === ' '
          ? (vars.randomPassword += randomChar)
          : i--
      } else {
        vars.randomPassword += randomChar
      }
    }
  }
</script>

<main>
  <div class="container">
    <h2>RandomPWD</h2>
    <div class="wrapper">
      <div class="input-box">
        <input
          type="text"
          spellcheck="false"
          readonly
          bind:value={vars.randomPassword}
        />
        <Clipboard
          text={vars.randomPassword}
          let:copy
          on:copy={() => (vars.randomPassword = '✔️')}
        >
          <span
            on:click={copy}
            style={`display: ${vars.randomPassword === '' ? 'none' : ''}`}
            class="material-symbols-rounded"
          >
            copy_all
          </span>
        </Clipboard>
      </div>
      <div
        id={vars.range.value <= 8
          ? 'weak'
          : vars.range.value <= 16
          ? 'medium'
          : 'strong'}
        class="pass-indicator"
      />
      <div class="pass-length">
        <div class="details">
          <label for="range">Password Length</label>
          <span>{vars.range.value}</span>
        </div>
        <input
          id="range"
          type="range"
          style={`background: linear-gradient(to right, #d863bb ${percent}%, #DFDFDF ${percent}%)`}
          on:change={generatePassword}
          min="1"
          max="30"
          bind:value={vars.range.value}
          step="1"
        />
      </div>
      <div class="pass-settings">
        <label for="settings">Password Settings</label>
        <ul id="settings" class="options">
          <li class="option">
            <input
              type="checkbox"
              id="lowercase"
              bind:checked={vars.lowercase}
            />
            <label for="lowercase">Lowercase <b>(</b>a-z<b>)</b></label>
          </li>
          <li class="option">
            <input
              type="checkbox"
              id="uppercase"
              bind:checked={vars.uppercase}
            />
            <label for="uppercase">Uppercase <b>(</b>A-Z<b>)</b></label>
          </li>
          <li class="option">
            <input type="checkbox" id="numbers" bind:checked={vars.numbers} />
            <label for="numbers">Numbers <b>(</b>0-9<b>)</b></label>
          </li>
          <li class="option">
            <input type="checkbox" id="symbols" bind:checked={vars.symbols} />
            <label for="symbols">Symbols <b>(</b>!-$^+<b>)</b></label>
          </li>
          <li class="option">
            <input
              type="checkbox"
              id="exc-duplicate"
              bind:checked={vars.duplicates}
            />
            <label for="exc-duplicate">Exclude Duplicates</label>
          </li>
          <li class="option">
            <input type="checkbox" id="spaces" bind:checked={vars.spaces} />
            <label for="spaces">Include Spaces</label>
          </li>
        </ul>
      </div>
      <button on:click={generatePassword} class="generate-btn"
        >Generate Password</button
      >
    </div>
  </div>
</main>
