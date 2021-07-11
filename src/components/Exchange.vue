<template>
	<div>
		<h1>VueJs Currency Exchange App</h1>
		<div class="container">
			<div class="row">
				<select v-model="currency_one" @change="calculateResults">
					<option
						v-for="country in countries"
						:key="country"
						:value="country"
					>
						{{ country }}
					</option>
				</select>

				<input
					type="number"
					v-model="amount_one"
					@input="calculateResults"
				/>
			</div>
			<div class="row centered">
				<button @click="switchCurrency">Switch</button>
				<h4>{{ baseRate }}</h4>
			</div>
			<div class="row">
				<select v-model="currency_two" @change="calculateResults">
					<option
						v-for="country in countries"
						:key="country"
						:value="country"
					>
						{{ country }}
					</option>
				</select>

				<input
					type="number"
					v-model="amount_two"
					@input="calculateResults"
				/>
			</div>
			<div class="row">
				<h4 id="lastlyUpdated">Lastly Updated: {{ lastlyUpdated }}</h4>
			</div>
		</div>
	</div>
</template>

<script>
import { ref } from "vue";
import axios from "axios";
import countries from "./countries.js";

export default {
	setup() {
		const currency_one = ref(countries.EUR);
		const amount_one = ref(1);

		const currency_two = ref(countries.USD);
		const amount_two = ref(0);

		const baseRate = ref("");

		const lastlyUpdated = ref("");

		const calculateResults = async () => {
			if (amount_one.value <= 0) {
				amount_two.value = 0;
				return;
			}

			const api_key = "9809b1cec3fb53ce2b3f3f6a";
			const url = `https://v6.exchangerate-api.com/v6/${api_key}/latest/${currency_one.value}`;

			try {
				const response = await axios.get(url);
				const rate = response.data.conversion_rates[currency_two.value];
				amount_two.value = (amount_one.value * rate).toFixed(2);
				lastlyUpdated.value = response.data.time_last_update_utc;
				baseRate.value = `1 ${currency_one.value} = ${rate} ${currency_two.value}`;
			} catch (error) {
				console.error("NO respose from API", error);
			}
		};

		const switchCurrency = () => {
			if (amount_one.value <= 0) {
				amount_one.value = 1;
			}

			const temp = currency_one.value;
			currency_one.value = currency_two.value;
			currency_two.value = temp;

			calculateResults();
		};

		// Initialize exchange
		calculateResults();

		return {
			calculateResults,
			switchCurrency,
			countries,
			currency_one,
			amount_one,
			currency_two,
			amount_two,
			lastlyUpdated,
			baseRate,
		};
	},
};
</script>

<style lang="scss" scoped>
.container {
	display: flex;
	flex-direction: column;
	align-items: center;

	.row {
		display: flex;
		&.centered {
			align-items: center;
		}

		select,
		input {
			padding: 5px;
			margin: 2px;
		}

		input {
			font-size: 18px;
			border: 1px solid rgba(0, 0, 0, 0.5);
			outline: none;
		}

		button {
			margin: 20px;
			padding: 9px;
			font-size: 18px;
			background: #5fbaa7;
			color: #fff;
			border: none;
			outline: none;
		}
	}
}
</style>
