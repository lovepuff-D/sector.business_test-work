<template>
	<SearchField @changeSearchField="changeSearchField"></SearchField>
	<Table :textToFind="fieldOfSearch"></Table>
</template>

<script>
    import SearchField from "@/components/SearchField";
    import Table from "@/components/Table";

    export default {
        name: 'App',
        components: {
            SearchField, Table
        },
        data() {
            return {
                fieldOfSearch: '',
            }
        },
        methods: {
            changeSearchField(text) {
                this.fieldOfSearch = text
            }
        }
    }
</script>

<style lang="scss">
	@import url('https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,400;0,500;1,500;1,700&display=swap');

	#app {
		font-family: 'Roboto', sans-serif, Avenir, Helvetica, Arial, sans-serif;
		font-weight: 500;
		font-size: 14px;
		-webkit-font-smoothing: antialiased;
		-moz-osx-font-smoothing: grayscale;
	}

	* {
		box-sizing: border-box;
	}

	body {
		margin: 0;
		padding: 55px 75px;

		@media screen and (max-width: 960px) {
			padding: 25px;
		}
	}

	button {
		cursor: pointer;
	}
</style>
