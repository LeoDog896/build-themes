<script lang="ts">
  import themes from "$lib/themes"

  let filter: string = "";

  // Creates a function that checks if a input matches the name.
  // For example, __a_ will match the string "that", and _al_ will match the string "ball"
  // _ is a wildcard -- if no wildcard are specified, it will match the characters set.
  // For example, "that" will match "that".
  // This function always ignores case.
  function matches(input: string, match: string): boolean {
    const lowercaseInput = input.toLowerCase();
    const lowercaseMatch = match.toLowerCase();

    for (let i = 0; i < input.length; i++) {
      if ((lowercaseInput[i] == "_" && lowercaseMatch[i]) || lowercaseInput[i] == lowercaseMatch[i]) {
        continue;
      } else {
        return false;
      }
    }

    return true;
  }

  $: filteredThemes = filter ? themes.filter(theme => matches(filter, theme)) : themes

  $: [small, large] = filteredThemes.reduce((result, element) => {
      result[element.length == filter.length ? 0 : 1].push(element); // Determine and push to small/large arr
      return result;
    }, [[], []]);  

  // sort alphabetically then put entries that are theme.length != filter.length
  $: sortedThemes = filteredThemes.sort((a, b) => a.localeCompare(b)).sort(a => a.length == filter.length ? -1 : 1)
</script>

<div class="text-center m-8 w-[100vw - 4rem]">
  <input placeholder="Search (_ is wildcard)" class="px-2 py-2 mb-4 w-1/2 border-b border-gray-800 border-dotted" bind:value={filter}>
  <div class="flex flex-col w-full">
    {#if sortedThemes.length > 0}
      {#each sortedThemes as theme}
        <p class="basis-1/3" class:text-gray-600={theme.length != filter.length}>{theme}</p>
      {/each}
    {:else}
      <p>No themes found. <a class="text-blue-400 hover:text-blue-500 underline" href="https://github.com/LeoDog896/build-themes">Feel free to add a new one!</a></p>
    {/if}
  </div>
</div>