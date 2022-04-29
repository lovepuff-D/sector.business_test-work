<template>
	<div class="search-field">
		<input v-model="fieldOfSearch"
			   @input="changeSearchField"
			   type="text"
			   placeholder="Поиск"
			   class="search-field__input">
	</div>
</template>

<script>
    export default {
        name: "SearchField",
        data() {
            return {
                fieldOfSearch: '',
            }
        },
        methods: {
            changeSearchField() {
                this.$emit('changeSearchField', this.fieldOfSearch)
            }
        }
    }
</script>

<style lang="scss" scoped>
	.search-field {
		$padding: 25px;

		position: relative;
		width: 40vw;

		@media screen and (max-width: 960px) {
			width: 100%;
		}

		&__input {
			width: 100%;

			padding: 15px $padding;

			font-size: 14px;

			color: white;
			background-color: #5A5C66;

			border: none;
			outline: none;

			&::placeholder {
				color: #B2B7BF;
			}
		}


		&::after {
			content: "";
			display: block;

			position: absolute;
			top: 50%;
			right: $padding;
			z-index: 15;

			transform: translateY(-50%);

			background: url("@/components/img/search-icon.png") no-repeat;

			width: 21px;
			height: 21px;

		}
	}
</style>