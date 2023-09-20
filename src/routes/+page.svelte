<div id="content">
  <h1>Hexdump Helper</h1>
  <textarea id="input_field" name="input_field" placeholder="Paste hexdump here" bind:value on:select={updateResults} rows="25" cols="125" />
  <p/>
  <button on:click={strip}>Strip byte markers</button>
  <p>Selected bytes: {hex.split(" ").length}</p>
  <hr>
  <p>Hex:</p>
  <p>{hex}</p>
  <hr>
  <p>Int:</p>
  <p>{intResult}</p>
  <hr>
  <p>Str:</p>
  <p>{strResult}</p>
</div>
<script>
  let value = `LOGGED TRANSACTION CONTENTS :
(   0 -  34 ) :  B0 02 2B 2C 39 EE 00 00 09 34 00 01 00 00 00 00 00 08 00 00 00 00 00 00 00 00 00 00 00 95 63 FD 00 AD 87
(  35 -  69 ) :  0D 00 15 81 29 05 00 00 00 00 00 00 40 E5 03 65 FD C2 9D 00 76 00 68 7B 02 00 16 53 00 00 09 00 00 00 96
(  70 - 104 ) :  02 04 00 00 00 01 10 24 00 00 00 00 00 00 09 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
( 105 - 139 ) :  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 40 E5 03 65 43
( 140 - 174 ) :  E5 03 65 AB 84 37 97 8A 01 00 00 5A 90 37 97 8A 01 00 00 01 00 0A 00 00 00 00 00 00 00 00 00 00 00 00 00
( 175 - 209 ) :  00 00 00 00 00 00 00 00 96 A7 C9 96 8A 01 00 00 23 00 01 34 D7 DC F9 13 01 00 00 02 00 97 00 00 00 00 00
( 210 - 244 ) :  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 4E 42 34 33 35 39 37 37 32 38 34 33 55 00 00 00 00
( 245 - 279 ) :  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 4E 42 34 33 35 39 37 37 32 38 34 33 55 00 00 00 00 00 00
( 280 - 314 ) :  00 00 00 00 00 00 00 00 00 00 00 00 00 00 4F 6E 64 65 72 6B 6F 76 E1 20 4B 61 72 6F 6C ED 6E 61 00 00 00
( 315 - 349 ) :  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
( 350 - 384 ) :  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 48 71 00 00 00 00 00 00
( 385 - 419 ) :  00 00 00 00 2B 2C 00 00 D5 17 00 00 51 23 09 00 00 00 00 00 00 00 00 00 00 00 00 00 D1 0A C7 02 00 00 00
( 420 - 454 ) :  00 40 E5 03 65 01 02 00 00 00 48 71 00 00 04 00 00 00 00 4B 55 52 DD 52 4E CD 20 50 4F 43 48 D9 5A 4B 41
( 455 - 489 ) :  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 44 4F 50 4C 41 54 4E C9 00 00 00 00 00 00 00 00
( 490 - 524 ) :  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 56 45 C8 45 52 4E CD 20 50 4F 43 48 D9 5A 4B 41
( 525 - 559 ) :  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 5A 50 52 4F 53 54 D8 45 44 4B 4F 56 C1 4E CD 20
( 560 - 594 ) :  4F 42 4A 45 44 4E C1 56 4B 59 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
( 595 - 629 ) :  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
( 630 - 664 ) :  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 6E 75 6C 6C 00 00 37 36 37 30 35 00 00 00 00 00
( 665 - 687 ) :  00 00 00 00 00 00 00 00 00 00 00 01 02 00 00 00 00 00 00 00 00 00 00 `
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