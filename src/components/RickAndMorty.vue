<script>
import axios from 'axios'

export default {
	data() {
		return {
			characters: [],
			filteredCharacters: [],
			searchTerm: '',
			activeFilter: 'All',
		}
	},
	mounted() {
		this.fetchCharacters('https://rickandmortyapi.com/api/character/').then(
			() => {
				this.filteredCharacters = this.characters
			}
		)
	},
	methods: {
		fetchCharacters(url) {
			return new Promise((resolve, reject) => {
				axios
					.get(url)
					.then(response => {
						this.characters = this.characters.concat(response.data.results)

						if (response.data.info.next) {
							this.fetchCharacters(response.data.info.next).then(resolve)
						} else {
							resolve()
						}
					})
					.catch(error => {
						console.error('Error al obtener la lista de personajes:', error)
						reject(error)
					})
			})
		},

		filterByStatus(status) {
			const lowerCaseStatus = status.toLowerCase()
			this.activeFilter = status
			if (lowerCaseStatus === 'all') {
				this.filteredCharacters = this.characters
			} else {
				this.filteredCharacters = this.characters.filter(
					character => character.status.toLowerCase() === lowerCaseStatus
				)
			}
		},

		filterByName() {
			const searchTerm = this.searchTerm.toLowerCase()
			this.filteredCharacters = this.characters.filter(character =>
				character.name.toLowerCase().includes(searchTerm)
			)
		},
	},
}
</script>

<template>
	<section>
		<h1>The Rick and Morty API <br /><br /></h1>

		<div class="filter">
			<div
				class="item"
				:class="{ active: activeFilter === 'All' }"
				@click="filterByStatus('All')"
			>
				All
			</div>
			<div
				class="item"
				:class="{ active: activeFilter === 'Alive' }"
				@click="filterByStatus('Alive')"
			>
				Alive
			</div>
			<div
				class="item"
				:class="{ active: activeFilter === 'Dead' }"
				@click="filterByStatus('Dead')"
			>
				Dead
			</div>
			<div
				class="item"
				:class="{ active: activeFilter === 'Unknown' }"
				@click="filterByStatus('Unknown')"
			>
				Unknown
			</div>
		</div>

		<input
			type="text"
			placeholder="Поиск по имени"
			v-model="searchTerm"
			@input="filterByName"
		/>

		<div v-if="filteredCharacters.length > 0" class="container-cards">
			<ul class="card-list">
				<li
					v-for="character in filteredCharacters"
					:key="character.id"
					class="card"
				>
					<div class="image">
						<img :src="character.image" :alt="character.name" />
					</div>

					<h2>{{ character.name }}</h2>

					<div class="status">
						<span
							:class="
								character.status == 'Alive'
									? 'alive'
									: character.status == 'Dead'
									? 'dead'
									: 'unknown'
							"
						>
						</span>
						<span>{{ character.status }} - {{ character.species }}</span>
					</div>

					<div class="information">
						<span>Gender: {{ character.gender }}</span>
						<span>Origin: {{ character.origin.name }}</span>
						<span>Location: {{ character.location.name }}</span>
					</div>
				</li>
			</ul>
		</div>
	</section>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap');

:root {
	--background-card: #2d2d2d;
	--text-orange: #f0932b;
	--text-gray: #d1ccc0;
	--rick-green: #7bed9f;
	--morty-yellow: #f9ca24;
	--portal-green: #00b894;
	--dark-blue: #273c75;
	--bright-pink: #e84393;
}

body {
	background-color: #1e272e;
	font-family: 'Press Start 2P', cursive;
	color: #ffffff;
}

section {
	display: flex;
	justify-content: center;
	align-items: center;
	flex-direction: column;
	margin: 0rem 10rem;
}

section > h1 {
	text-align: center;
	font-size: 45px;
	margin-top: 4rem;
	color: var(--portal-green);
	text-shadow: 2px 2px var(--dark-blue);
}

section .filter {
	width: 25%;
	display: flex;
	justify-content: space-around;
	align-items: center;
	background-color: var(--background-card);
	padding: 0.9rem;
	font-size: 15px;
	border-radius: 0.5rem;
	cursor: pointer;
	margin-bottom: 1rem;
	color: var(--text-gray);
	transition: background-color 200ms ease-in-out;
}

section .filter:hover {
	color: var(--text-orange);
	background-color: var(--portal-green);
}

section > input {
	width: 25%;
	padding: 0.9rem;
	border-radius: 0.5rem;
	border: none;
	margin-bottom: 1rem;
	background-color: var(--background-card);
	color: var(--text-gray);
	font-family: 'Press Start 2P', cursive;
}

section > input::placeholder {
	color: var(--text-gray);
}

section .container-cards {
	width: 100%;
	display: flex;
	justify-content: center;
	align-items: center;
}

.card-list {
	list-style: none;
	padding: 0;
	display: flex;
	flex-wrap: wrap;
	justify-content: space-around;
}

.card {
	width: 20%;
	margin: 1%;
	border-radius: 1rem;
	background-color: var(--background-card);
	box-sizing: border-box;
	text-align: center;
	overflow: hidden;
	transition: transform 200ms ease-in-out;
	cursor: pointer;
}
.card:hover {
	transform: scale(1.05);
}
.card .image {
	width: 100%;
}
.card .image > img {
	width: 100%;
}
.card > h2 {
	margin: 5px;
}

.status {
	margin: 5px;
	display: flex;
	justify-content: center;
	align-items: center;
	flex-direction: row;
}
.status span {
	color: var(--text-gray);
}
.status span:first-child {
	width: 10px;
	height: 10px;
	border-radius: 50%;
	margin-right: 0.5rem;
}
.alive {
	background-color: green;
}
.dead {
	background-color: red;
}
.unknown {
	background-color: white;
}

.card .information {
	display: flex;
	justify-content: center;
	align-items: center;
	flex-direction: column;
}
.card .information > span {
	color: var(--text-gray);
	margin: 2px;
}
.card .information > span:last-child {
	margin-bottom: 20px;
}
.item.active {
	color: orange;
}
</style>
