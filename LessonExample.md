# 1. Outline

1. Quasar Components
	1. How to use a Quasar component
2. Composition API & Reactive Variables with `ref()`
	1. What is a `ref()``?
	2. Setting a `ref` and connecting it to a Quasar component
	3. Use Vue Dev tools to inpect
3. Watchers
	1. Quasar component events
	2. Set a watcher
4. Events
	1. What is an `emit`
	2. How to listen and act from parent

# 2. Exercise

Use the `main` branch as the starting point for this exercise: https://github.com/ielvisd/musicPlayerDemo

- Quasar Components
	- Task 1 - Create an `Input` dropdown Quasar component to use as a search filter.
		- https://quasar.dev/vue-components/input
		- Use 'Search audio tracks' as the placeholder and set the v-model to `searchText`.
		- Set a `debounce` of 500 to allow some time for the user to finish typing before making an API call
- Composition API and Reactive Variables with ref()
	- Task 2 - Hook up this new input component to a `searchText` reactive ref.
		- Add `searchText` as a reactive ref.
					- Use type `string`
				- Use your Dev tools to verify that this is working as expected
- Watchers
	- Task 3 - Use Input's @update method to console.log everytime we finish typing into the search input
			- Use the component's `@update` method to log `('updating, searchText is: ', searchText)` when a user finishes typing and observe if working correctly.
	- Task 4 - Use a watcher which works for any Vue component
			- Now, delete this method and use a `watcher` to accomplish the same thing.
- Events
	- Task 5 - Emit the search text to the parent of `AudioTrackList` and retrieve the new tracks based on this search.
		- You will get practice creating a new piece of reactive state in this parent component to track the search term.
		- Use the date filter as an example.

- Next Exercise
	- Use a computed property to sort the list based alphabetically

# 3. Article - Compare & contrast using a Quasar component's native events vs a watcher on a reactive

**Step 1** - Quasar comes with prebuilt performant and responsive components right out of the box. For this exercise we will be using the Quasar Input (https://quasar.dev/vue-components/input) component. Add it to the top of the `AudioTracksList` component, after changing the defaults as requested in the exercise you shoudl have something like this:

```<q-input debounce="500" v-model="searchText" label="Search audio tracks" />```

**Step 2** - Hooking the input value in our component to a reactive object is as simple as using the `ref()` function. This function returns an object with a `.value` property which we can use to "unwrap" or access any value type that we've wrapped with this function.

In the case of our search field. We will add this line to our `script`

`const searchText = ref<string>('');`

`<string>` This is how we use TypeScript to type our `searchText` value as type `string`.

This would be a great time to check the dev tools to see that everything is updating as expected.

Step 3 - Quasar components have events that can be used for different things. It's always good to check the docs for what can be done with them. In this case we can use the `@update` event to console.log everytime we finish typing. We will be using this functionality in the next step.

```
<q-input @update="logAfterTypingFunction" debounce="500" v-model="searchText" label="Search audio tracks" />
```

Step 4 - Another way of accomplishing this is to use a `watch` function. These functions are used to trigger a callback whenever a reactive piece of state changes. Here is how we can use it to accomplish the same thing as above:

```
watch(searchText, (newSearchText) => {
logAfterTypingFunction()
})
```

Step 5 - We need a way to share the `searchText` with the parent component to run a new search and retrieve new tracks. This is where events come into play. We can declare events using the `defineEmits()` macro in our script setup.

In `AudioTracksList.vue` already have one for date so adding another one will look like this:
```
const emit = defineEmits<{
	(e: 'setNewDate', newDateModel: object): void,
	(e: 'setSearchText', newSearchText: string): void,
}>()
```

We then update our watcher to not log but to emit the search text:
```
watch(searchText, (newSearchText) => {
	emit('setSearchText', newSearchText)
})
```

Similarly to our setNewDate method we can listen to this event in the parent component and run a new search, you also have to update the `useQuery` with this variable instead of it being hardcoded to an empty string:

```
<audio-tracks-list
	@setNewDate="setDateRange"
	@setSearchText="setSearchText"
	title="Best selling audio NFTs"
	:tracks="audioNFTs">
</audio-tracks-list>
```

```
const searchText = ref<string>('');

const setSearchText = (value: string) => {
	searchText.value = value;
	getAudioRankings(dateRange.value, searchText);
};

const getAudioRankings = async (dateFilter: string, searchFilter: string) => {
const { result } = useQuery(gql`
query Ranks{
ranks(
filter: "${dateFilter.value}",
category: "AUDIO",
search: "${searchFilter.value}")
...
}`)
```

And with that we should have a functioning search tool now!

We went over Quasar components, reactivity with `ref()`, TypeScript, Quasar component events, `watch`, events & even a bit of GraphQL. Let me know if you have any quesitons or comments.

Solution in `solution` branch. https://github.com/ielvisd/musicPlayerDemo

# 4. Video (optional)
https://youtu.be/Z8Hy3w0AfSY

# 5. Time

For this part I spent about 2 hours coding, 1 hour writing and 30 minutes recording a solution walkthrough when it was all done.