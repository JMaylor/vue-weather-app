<template>
	<main
		:class="typeof weather.location != 'undefined' ? weather.current.condition.text.toLowerCase().replace(/\s+/g, '-') + '-' + weather.current.is_day : ''"
	>
		<div class="shader p-0">
			<p class="text-white mb-0">
				made by
				<a href="https://maylor.io" target="_blank" class="text-white">maylor.io</a>
			</p>
			<b-container class="text-light">
				<b-row class="justify-content-center">
					<b-col cols="12" sm="10" md="8" lg="5" xl="4">
						<b-input
							id="search-bar"
							type="text"
							placeholder="Search..."
							v-model="query"
							@keypress.enter="fetchWeather"
						></b-input>
					</b-col>
				</b-row>
			</b-container>

			<b-container v-if="weather.location" class="text-light">
				<b-row class="mb-0">
					<b-col>
						<h1>{{ weather.location.name }}</h1>
					</b-col>
				</b-row>

				<b-row class="mb-5">
					<b-col>{{ formatDate() }}</b-col>
				</b-row>

				<b-row class="justify-content-center">
					<b-col>
						<h2>{{ weather.current.condition.text }}</h2>
					</b-col>
				</b-row>

				<b-row class="justify-content-center align-items-center">
					<b-col cols="6" class="pr-0">
						<h1 class="display-1 text-right">{{Math.round(weather.current.temp_c,0) }}°</h1>
					</b-col>
					<b-col cols="6" class="pl-5">
						<b-row class="align-items-center">
							<font-awesome-icon icon="long-arrow-alt-up" style="font-size: inherit" />
							{{ Math.round(weather.forecast.forecastday[0].day.maxtemp_c,0) }}°
						</b-row>
						<b-row class="align-items-center">
							<font-awesome-icon icon="long-arrow-alt-down" style="font-size: inherit" />
							{{ Math.round(weather.forecast.forecastday[0].day.mintemp_c,0) }}°
						</b-row>
					</b-col>
				</b-row>

				<b-row class="justify-content-center">
					<b-col cols="6" sm="4" md="3" lg="2" class="p-1">
						<b-col cols="12" class="detail-card">
							<font-awesome-icon icon="thermometer-half" />
							<p class="detail-label mb-0">Feels like</p>
							{{Math.round(weather.current.feelslike_c,0) }}°
						</b-col>
					</b-col>
					<b-col cols="6" sm="4" md="3" lg="2" class="p-1">
						<b-col cols="12" class="detail-card">
							<font-awesome-icon icon="wind" />
							<p class="detail-label mb-0">Wind</p>
							{{Math.round(weather.current.wind_mph,0) }} mph
						</b-col>
					</b-col>
					<b-col cols="6" sm="4" md="3" lg="2" class="p-1">
						<b-col cols="12" class="detail-card">
							<font-awesome-icon icon="tint" />
							<p class="detail-label mb-0">Humidity</p>
							{{ weather.current.humidity }}%
						</b-col>
					</b-col>
					<b-col cols="6" sm="4" md="3" lg="2" class="p-1">
						<b-col cols="12" class="detail-card">
							<font-awesome-icon icon="tachometer-alt" />
							<p class="detail-label mb-0">Pressure</p>
							{{ Math.round(weather.current.pressure_in,0) }} in
						</b-col>
					</b-col>
					<b-col cols="6" sm="4" md="3" lg="2" class="p-1">
						<b-col cols="12" class="detail-card">
							<font-awesome-icon icon="eye" />
							<p class="detail-label mb-0">Visibility</p>
							{{ weather.current.vis_miles }} miles
						</b-col>
					</b-col>
					<b-col cols="6" sm="4" md="3" lg="2" class="p-1">
						<b-col cols="12" class="detail-card">
							<font-awesome-icon icon="sun" />
							<p class="detail-label mb-0">UV</p>
							{{ weather.current.uv }}
						</b-col>
					</b-col>
				</b-row>
			</b-container>
			<b-container v-else class="text-light">
				<b-row>
					<b-col>
						<h1>Unable to find location</h1>
					</b-col>
				</b-row>
			</b-container>

			<b-navbar fixed="bottom" class="text-light justify-content-center detail-card">
				Powered by
				<a href="https://www.weatherapi.com/" title="Free Weather API">WeatherAPI.com</a>
			</b-navbar>
		</div>
	</main>
</template>

<script>
	const moment = require('moment');

	export default {
		name: "Weather",
		props: {
			msg: String,
		},
		data() {
			return {
				api_key: process.env.VUE_APP_APIKEY,
				url_base: "https://api.weatherapi.com/v1/forecast.json?key=",
				query: "",
				weather: {},
			};
		},
		methods: {
			clearInput() {
				document.querySelector(".search-bar").value = "";
			},
			fetchWeather() {
				if (this.query == "") {
					return null;
				}
				fetch(`${this.url_base}${this.api_key}&q=${this.query}&days=3`)
					.then((res) => res.json())
					.then((json) => {
						if (json.error) {
							this.weather = {};
						} else {
							this.weather = json;
						}
						this.query = "";
						this.formatDate();
					})
					.catch((err) => {
						console.log(err);
					});
			},
			formatDate() {
				const m = new moment(this.weather.current.last_updated);
				return m.format("ddd, h:mm A");
			}
		},
		beforeMount() {
			window.navigator.geolocation.getCurrentPosition(
				(position) => {
					// get the latitude and longitude returned
					const lat = position.coords.latitude;
					const long = position.coords.longitude;

					// set the query and fetch weather
					this.query = `${lat},${long}`;
					this.fetchWeather();
				},
				() => {
					this.query = 'London';
					this.fetchWeather();
				},
				{
					enableHighAccuracy: true,
					maximumAge: 0,
				}
			);
		},
	};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
	@import url("https://fonts.googleapis.com/css2?family=Quicksand:wght@300;400&display=swap");

	* {
		font-family: "Quicksand", sans-serif;
	}

	template {
		margin: 0;
	}

	main {
		text-align: center;
		font-family: "montserrat", sans-serif;
		background-image: url("https://images.unsplash.com/photo-1573166331058-c9e7ef82687d?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=701&q=80");
		background-size: cover;
		background-position: center;
		transition: 0.4s;
		min-height: 100vh;
		margin: 0;
	}

	/* day-thunder */
	main[class*="thunder"][class$="1"] {
		background-image: url("https://images.unsplash.com/photo-1579004464832-0c014afa448c?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1024&q=80");
	}

	/* day-snow */
	main[class*="snow"][class$="1"],
	main[class*="sleet"][class$="1"],
	main[class*="blizzard"][class$="1"],
	main[class*="ice"][class$="1"] {
		background-image: url("https://images.unsplash.com/photo-1516820208784-270b250306e3?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=564&q=80");
	}

	/* day-fog */
	main[class*="fog"][class$="1"],
	main[class*="mist"][class$="1"] {
		background-image: url("https://images.unsplash.com/photo-1510596713412-56030de252c8?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=634&q=80");
	}

	/* day-rain */
	main[class*="rain"][class$="1"],
	main[class*="drizzle"][class$="1"] {
		background-image: url("https://images.unsplash.com/photo-1486016006115-74a41448aea2?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1024&q=80");
	}

	/* day-cloudy */
	main[class*="cloudy"][class$="1"],
	main[class*="overcast"][class$="1"] {
		background-image: url("https://images.unsplash.com/photo-1527708676371-14f9a9503c95?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=675&q=80");
	}

	/* day-sun is default */

	/* night-thunder */
	main[class*="thunder"][class$="0"] {
		background-image: url("https://images.unsplash.com/photo-1492011221367-f47e3ccd77a0?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=634&q=80");
	}

	/* night-snow */
	main[class*="snow"][class$="0"],
	main[class*="sleet"][class$="0"],
	main[class*="blizzard"][class$="0"],
	main[class*="ice"][class$="0"] {
		background-image: url("https://images.unsplash.com/photo-1547242651-45e06cae2491?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=634&q=80");
	}

	/* night-fog */
	main[class*="fog"][class$="0"],
	main[class*="mist"][class$="0"] {
		background-image: url("https://images.unsplash.com/photo-1464457312035-3d7d0e0c058e?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1024&q=80");
	}

	/* night-rain */
	main[class*="rain"][class$="0"],
	main[class*="drizzle"][class$="0"] {
		background-image: url("https://images.unsplash.com/photo-1534274988757-a28bf1a57c17?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=675&q=80");
	}

	/* night-cloudy */
	main[class*="cloudy"][class$="0"],
	main[class*="overcast"][class$="0"] {
		background-image: url("https://images.unsplash.com/photo-1479156661942-92cd989cdb56?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1024&q=80");
	}

	/* night-clear */
	main[class*="clear"][class$="0"] {
		background-image: url("https://images.unsplash.com/photo-1573166331058-c9e7ef82687d?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=701&q=80");
	}

	.sunny-1 {
		background-image: url("https://images.unsplash.com/photo-1595174279427-74191a9e2a3e?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1024&q=80");
	}

	.shader {
		min-height: 100vh;
		padding: 25px;
		background-color: rgba(0, 0, 0, 0.5);
	}

	#search-bar {
		border-radius: 16px 0px 16px 0px;
		-webkit-transition: all 0.3s ease-out;
		transition: all 0.3s ease-out;
	}

	#search-bar:focus {
		border-radius: 0px 16px 0px 16px;
		box-shadow: 0px 0px 10px white;
	}

	.svg-inline--fa {
		font-size: 30px;
	}

	.detail-card {
		background: rgba(255, 255, 255, 0.164);
		border-radius: 5px;
		padding: 5px;
	}

	.detail-label {
		font-size: 14px;
	}

	.credits {
		text-align: center;
		background-color: rgba(255, 255, 255, 0.5);
		border-radius: 4px;
		position: fixed;
		bottom: 10px;
	}
</style>