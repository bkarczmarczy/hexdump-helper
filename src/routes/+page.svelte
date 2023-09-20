<div id="content">
  <h1>Hexdump Helper</h1>
  <ol>
    <li>Paste hexdump into input field</li>
    <li>Strip the byte count markers by clicking the button below</li>
    <li>Select bytes to decode</li>
  </ol>
  <textarea id="input_field" name="input_field" placeholder="Paste hexdump here" bind:value on:select={updateResults} rows="25" cols="125" />
  <p/>
  <button on:click={strip}>Strip byte markers</button>
  <p>Selected bytes: {hex.split(" ").length}</p>
  <hr>
  <p><b>Numbers</b></p>
  <p>Hex: {hex}</p>
  <p>Int: {intResult}</p>
  <hr>
  <p><b>String</b></p>
  <p>{strResult}</p>
</div>
<script>
  let value = ""
  let intResult = ""
  let strResult = ""
  let hex = ""

  // strip()

  function strip(e) {
    let temp = ""
    value.split('\n').forEach(s => {
      temp += s.slice(s.indexOf(':') + 1).trim()
      temp += '\n'
    })
    value = temp
  }

  function updateResults(e) {
    intResult = ""
    strResult = ""
    const selectedText = e.target.value.substring(
      e.target.selectionStart,
      e.target.selectionEnd,
    );

    let parts = selectedText.trim().split(" ")
    let combined = combineParts(parts)
    hex = combined
    intResult = parseInt(combined.replaceAll(" ", ""), 16)
    strResult = hex.split(" ").map(s => parseInt(s, 16)).map(s => String.fromCharCode(s)).reverse().join("")
  }

  function combineParts(parts) {
    let filtered = parts.reverse().filter(s => s.length == 2)
    let startIndex = 0
    for (let i = 0; i < filtered.length; i++) {
      if (filtered[i] != "00") {
        startIndex = i
        break
      }
    }
    return filtered.slice(startIndex).join(" ")
  }
</script>

<style>
  #content {
    max-width: 900px;
    word-wrap: break-word;
  }
</style>