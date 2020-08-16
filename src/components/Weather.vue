<template>
	<main :class="typeof weather.location != 'undefined' ? weather.current.condition.text.toLowerCase().replace(/\s+/g, '-') + '-' + weather.current.is_day : ''">
		<div class="shader">
			<div class="search-box">
				<input 
					type="text" 
					placeholder="Search..." 
					class="search-bar"
					v-model="query"
					@keypress.enter="fetchWeather"
				/>
			</div>
	
			<div class="weather-wrap" v-if="typeof weather.location != 'undefined'">
				<div class="location-box">
					<div class="location">{{ weather.location.name }}, {{ weather.location.country }}</div>
					<div class="date">{{ weather.today }}</div>
				</div>
	
				<div class="weather-box">
					<div class="temp">{{ Math.round(weather.forecast.forecastday[0].day.avgtemp_c,0) }}°C</div>
					<div class="weather">{{ weather.current.condition.text }}</div>
				</div>
			</div>
			<div class="credits">
				<div class="api">
					Powered by <a href="https://www.weatherapi.com/" title="Free Weather API">WeatherAPI.com</a>
				</div>
			</div>
		</div>
	</main>
</template>

<script>
export default {
	name: "Weather",
	props: {
		msg: String,
	},
	data () {
		return {
			// api_key: '75ee3f3deef5491fad395354200108',
			url_base: 'https://api.weatherapi.com/v1/forecast.json?key=',
			query: 'London',
			weather: {},
		}
	},
	methods: {
		clearInput () {
			document.querySelector('.search-bar').value = '';
		},
		fetchWeather () {
			fetch(`${this.url_base}${process.env.VUE_APP_API_KEY}&q=${this.query}&days=3`)
				.then(res => res.json())
				.then(json => {
					this.weather = json;
					this.query = '';
					const date = new Date(this.weather.current.last_updated);
					const days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];
					const months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'];
					let dateEnding;
					switch (date.getDate()) {
						case 1:
						case 21:
						case 31:
							dateEnding = 'st';
							break;
						case 2:
						case 22:
							dateEnding = 'nd';
							break;
						case 3:
						case 23:
							dateEnding = 'rd';
							break;
						default:
							dateEnding = 'th';
							break;
					}
					this.weather.today = `${days[date.getDay()]} ${date.getDate()}${dateEnding} ${months[date.getMonth()]} ${date.getFullYear()}`;
				});
		}
	},
	beforeMount () {
		this.fetchWeather();
	}
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
}

template {
	margin: 0;
}

.shader {
	min-height: 100vh;
	padding: 25px;
	background-color: rgba(0, 0, 0, 0.25);
}

main {
	text-align: center;
	font-family: "montserrat", sans-serif;
	background-image: url("~@/assets/night-clear.jpg");
	background-size: cover;
	background-position: center;
	transition: 0.4s;
	min-height: 100vh;
	margin: 0;
}

/* day-thunder */
main[class*="thunder"][class$="1"] {
	background-image: url("~@/assets/day-thunder.jpg");
}

/* day-snow */
main[class*="snow"][class$="1"], main[class*="sleet"][class$="1"], main[class*="blizzard"][class$="1"], main[class*="ice"][class$="1"] {
	background-image: url("~@/assets/day-snow.jpg");
}

/* day-fog */
main[class*="fog"][class$="1"], main[class*="mist"][class$="1"] {
	background-image: url("~@/assets/day-fog.jpg");
}

/* day-rain */
main[class*="rain"][class$="1"], main[class*="drizzle"][class$="1"] {
	background-image: url("~@/assets/day-rain.jpg");
}

/* day-cloudy */
main[class*="cloudy"][class$="1"], main[class*="overcast"][class$="1"] {
	background-image: url("~@/assets/day-cloudy.jpg");
}

/* day-sun is default */

/* night-thunder */
main[class*="thunder"][class$="0"] {
	background-image: url("~@/assets/night-thunder.jpg");
}

/* night-snow */
main[class*="snow"][class$="0"], main[class*="sleet"][class$="0"], main[class*="blizzard"][class$="0"], main[class*="ice"][class$="0"] {
	background-image: url("~@/assets/night-snow.jpg");
}

/* night-fog */
main[class*="fog"][class$="0"], main[class*="mist"][class$="0"] {
	background-image: url("~@/assets/night-fog.jpg");
}

/* night-rain */
main[class*="rain"][class$="0"], main[class*="drizzle"][class$="0"] {
	background-image: url("~@/assets/night-rain.jpg");
}

/* night-cloudy */
main[class*="cloudy"][class$="0"], main[class*="overcast"][class$="0"] {
	background-image: url("~@/assets/night-cloudy.jpg");
}

/* night-clear */
main[class*="clear"][class$="0"] {
	background-image: url("~@/assets/night-clear.jpg");
}

.sunny-1 {
	background-image: url("~@/assets/day-sunny.jpg");
}

.search-box .search-bar {
	display: block;
	width: 100%;
	padding: 15px;
	color: #313131;
	font-size: 20px;
	appearance: none;
	border: none;
	background: none;

	box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
	background-color: rgba(255, 255, 255, 0.5);
	border-radius: 0px 16px 0px 16px;
	transition: 0.4s;
}

.search-box .search-bar:focus {
	box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
	background-color: rgba(255, 255, 255, 0.75);
	border-radius: 16px 0px 16px 0px;
	outline-style: none;
}

.weather-wrap {
	margin-top: 30px;
}

.location-box .location {
	color: #FFF;
	font-size: 32px;
	font-weight: 500;
	text-align: center;
	text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date{
	color: #FFF;
	font-size: 20px;
	font-weight: 300;
	font-style: italic;
	text-align: center;
}

.weather-box {
	text-align: center;
}

.weather-box .temp {
	display: inline-block;
	padding: 10px 25px;
	color: #FFF;
	font-size: 102px;
	font-weight: 900;

	text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
	background-color: rgba(255, 255, 255, 0.25);
	border-radius: 16px;
	margin: 30px 0px;

	box-shadow: 3px 6px rgba(0, 0, 0, 0.25)
}

.weather-box .weather {
	color: #FFF;
	font-size: 48px;
	font-weight: 700;
	font-style: italic;
	text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.credits {
	text-align: center;
	background-color: rgba(255, 255, 255, 0.75);
	border-radius: 4px;
}

</style>