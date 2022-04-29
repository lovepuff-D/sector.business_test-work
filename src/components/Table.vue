<template>
	<div class="table">
		<div class="table__header">
			<div class="table__header_row"
				 @click="SortPostById">
				ID
				<div class="arrow-icon"
					 :class="{'reverse-icon' : flagForId}">
				</div>
			</div>
			<div class="table__header_row"
				 @click="SortPostByTitle">
				Заголовок
				<div class="arrow-icon"
					 :class="{'reverse-icon' : flagForTitle}">
				</div>
			</div>
			<div class="table__header_row"
				 @click="SortPostByDescription">
				Описание
				<div class="arrow-icon"
					 :class="{'reverse-icon' : flagBody}">
				</div>
			</div>
		</div>
		<div class="table__body">
			<transition-group name="flip-list">
				<div v-for="post in posts"
					 :key="post.id"
					 class="table__body_row body-row"
				>
					<div class="body-row__td">
						<div class="body-row__td_inner">{{post.id}}</div>
					</div>
					<div class="body-row__td">
						<div class="body-row__td_inner">{{post.title}}</div>
					</div>
					<div class="body-row__td">
						<div class="body-row__td_inner">{{post.body}}</div>
					</div>

				</div>
				<div v-if="posts.length === 0 && textToFind.length > 0">
					<h3 style="text-align: center; margin: 50px 0">Попробуйте найти что-нибудь другое</h3>
				</div>
			</transition-group>
		</div>
		<div class="table__pagination">
			<button @click="paginationPrev"
					class="table__pagination_prev-next"
			>Назад
			</button>
			<div class="table__pagination_num">
				<button v-for="index in maxPage"
						:key="index"
						@click="pagination(index)"
						:class="{'active-page' : page == index}"
						class="pagination-button">
					{{index}}
				</button>
			</div>
			<button @click="paginationNext"
					class="table__pagination_prev-next"
			>Далее
			</button>

		</div>
		<div v-if="posts.length === 0 && textToFind.length === 0"
			 class="lds-ring">
			<div></div>
			<div></div>
			<div></div>
			<div></div>
		</div>
	</div>
</template>

<script>
    import axios from "axios"

    export default {
        name: "Table",
        props: {
            textToFind: {
                type: String,
            },
        },
        data() {
            return {
                originalPosts: [],
                posts: [],

                flagForId: false,
                flagForTitle: false,
                flagBody: false,

                maxPage: 0,
                limitPage: 10,
                page: 1,
            }
        },
        methods: {
            findSomePost() {
                //Наблюдается через watch
                this.posts = this.originalPosts.filter(item =>
                    item.title.includes(this.textToFind)
                    || item.body.includes(this.textToFind)
                    || String(item.id).includes(this.textToFind))
            },

            //Сортировка
            SortPostById() {

                if (this.flagForId) {
                    this.posts.sort((a, b) => a.id - b.id)
                    this.flagForId = false
                } else {
                    this.posts.sort((a, b) => b.id - a.id)
                    this.flagForId = true
                }
            },
            SortPostByTitle() {

                if (!this.flagForTitle) {
                    this.posts.sort((a, b) => {
                        return a.title.localeCompare(b.title)
                    })
                    this.flagForTitle = true
                    // console.log(this.posts, 'sort by title')

                } else {
                    this.posts.sort((a, b) => a.title - b.title ? 1 : -1)
                    this.flagForTitle = false
                }
            },
            SortPostByDescription() {
                if (!this.flagBody) {
                    this.posts.sort((a, b) => {
                        return a.body.localeCompare(b.body)
                    })
                    this.flagBody = true
                    // console.log(this.posts, 'sort by body')

                } else {
                    this.posts.sort((a, b) => a.body - b.body ? 1 : -1)
                    this.flagBody = false
                }
            },
            //---
            async fetchPost() {
                this.maxPage = (await axios.get('https://jsonplaceholder.typicode.com/posts')).data.length / this.limitPage
                const response = await axios.get('https://jsonplaceholder.typicode.com/posts', {
                    params: {
                        _limit: this.limitPage,
                        _page: this.page,
                    }
                })

                this.posts = response.data
                this.originalPosts = response.data
                // console.log(this.posts)
            },

            //Пагинация
            pagination(index) {
                this.page = index
                this.fetchPost()
                location.hash = this.page;
            },
            paginationPrev(index) {
                if (this.page === 1) return
                this.page--
                this.fetchPost()
                location.hash = this.page;
            },
            paginationNext(index) {
                if (this.page >= this.maxPage) return
                this.page++
                this.fetchPost()
                location.hash = this.page;
            },
        },
        mounted() {
            this.fetchPost()
            location.hash = this.page;
        },
        watch: {
            "textToFind": {
                handler(newValue, oldValue) {
                    this.findSomePost()
                },
                deep: true
            }
        },
    }
</script>

<style lang="scss" scoped>

	.table {
		display: flex;
		flex-direction: column;

		min-height: 350px;
		min-width: 500px;

		margin: 25px 0;

		position: relative;

		&__header {
			display: flex;
			justify-content: space-between;

			width: 100%;

			background-color: #474955;

			text-align: center;
			font-weight: 600;

			&_row {
				position: relative;

				padding: 15px;

				color: white;
				transition: opacity .3s;

				cursor: pointer;

				.arrow-icon {
					display: inline-block;

					position: relative;
					height: 7px;


					&::after {
						content: "";
						display: block;

						position: absolute;
						top: 50%;
						right: -25px;
						transform: translateY(-50%);

						transition: transform .3s;

						background: url("@/components/img/arrow-up-icon.png") no-repeat;

						width: 12px;
						height: 7px;
					}

				}

				.reverse-icon {
					&::after {
						transform: rotate(-180deg) translateY(50%) !important;
					}
				}

				&:hover {
					opacity: .7;
				}

				&:first-child {
					width: 10%;
				}

				&:nth-child(2) {
					width: 45%;
				}

				&:last-child {
					width: 45%;
				}
			}


		}

		&__body {
			display: flex;
			flex-direction: column;
			justify-content: space-between;

			width: 100%;

			margin-bottom: 15px;

			.body-row {
				$border-local: 1px solid #E3E6EC;

				display: flex;
				justify-content: space-between;
				align-items: stretch;

				width: 100%;

				border: $border-local;
				border-bottom: none;

				&:last-child {
					border-bottom: $border-local;
				}

				&__td {
					font-size: 13px;

					&_inner {
						display: flex;
						justify-content: center;
						align-items: center;

						height: 100%;

						padding: 15px;

						text-align: center;
					}

					&:not(:last-child) {
						border-right: $border-local;
					}

					&:first-child {
						width: 10%;
					}

					&:nth-child(2) {
						width: 45%;
					}

					&:last-child {
						width: 45%;
					}

				}
			}
		}

		&__pagination {
			display: flex;
			justify-content: space-between;

			padding: 0 25px;

			button {
				background-color: transparent;

				border: none;

				font-size: 18px;

				transition: opacity .3s;

				&:hover {
					opacity: .7;
				}
			}

			.pagination-button {
				font-weight: 700;
				font-style: italic;
			}

			.table__pagination_prev-next {
				font-size: 24px;
			}
		}
	}

	.active-page {
		color: #7EBC3C;
	}

	//Анимация для сортировки
	.flip-list-move {
		transition: transform .4s;
	}

	//Прелоадер
	.lds-ring {
		display: inline-block;

		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);

		width: 80px;
		height: 80px;
	}

	.lds-ring div {
		box-sizing: border-box;
		display: block;
		position: absolute;
		width: 64px;
		height: 64px;
		margin: 8px;
		border: 8px solid #fff;
		border-radius: 50%;
		animation: lds-ring 1.2s cubic-bezier(0.5, 0, 0.5, 1) infinite;
		border-color: black transparent transparent transparent;
	}

	.lds-ring div:nth-child(1) {
		animation-delay: -0.45s;
	}

	.lds-ring div:nth-child(2) {
		animation-delay: -0.3s;
	}

	.lds-ring div:nth-child(3) {
		animation-delay: -0.15s;
	}

	@keyframes lds-ring {
		0% {
			transform: rotate(0deg);
		}
		100% {
			transform: rotate(360deg);
		}
	}

</style>