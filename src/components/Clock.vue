<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from 'vue';

const props = defineProps({
	address: String,
	weather: String,
	timezone: String
})

const date = ref(new Date());
const listTimezone = ref(Intl.supportedValuesOf('timeZone'));
const timezonSelected = ref(props.timezone);
let interval;

onMounted(() => {
	interval = setInterval(updateTime, 1000);
});

onBeforeUnmount(() => {
	clearInterval(interval);
});

function updateTime() {
	date.value = new Date(new Date().toLocaleString("en-US", { timeZone: timezonSelected.value }));
}

const hours = computed(() => {
	const hours = date.value.getHours();
	const minutes = date.value.getMinutes();
	return +(hours * 30).toFixed(3) + +((minutes / 60) * 30);
});

const minutes = computed(() => {
	const minutes = date.value.getMinutes();
	return (minutes * 6);
});

const seconds = computed(() => {
	const seconds = date.value.getSeconds();
	return (seconds * 6).toFixed(3);
});

const currentDate = computed(() => {
	const date = new Date()
	const day = date.getDate()
	const month = date.getMonth() + 1
	const year = date.getFullYear()
	return `${year}/${month}/${day}`
})

</script>

<template>
	<div>
		<div class="clock-wraper">
			<div class="clock">
				<div class="hand hour" :style="{ transform: `rotate(${hours}deg)` }"></div>
				<div class="hand minute" :style="{ transform: `rotate(${minutes}deg)` }"></div>
				<div class="hand second" :style="{ transform: `rotate(${seconds}deg)` }"></div>
				<div class="number number1">1</div>
				<div class="number number2">2</div>
				<div class="number number3">3</div>
				<div class="number number4">4</div>
				<div class="number number5">5</div>
				<div class="number number6">6</div>
				<div class="number number7">7</div>
				<div class="number number8">8</div>
				<div class="number number9">9</div>
				<div class="number number10">10</div>
				<div class="number number11">11</div>
				<div class="number number12">12</div>
			</div>
			<div class="select-wraper">
				<select v-model="timezonSelected">
					<option v-for="timezone in listTimezone" :key="timezone" :value="timezone">
						{{ timezone }}
					</option>
				</select>
			</div>
		</div>
		<div style="text-align: start;">
			<p>
				<b>Địa chỉ: </b> {{ props.address }}
			</p>
			<p>
				<b>Ngày: </b> {{ currentDate }}
			</p>
			<p>
				<b>Thời tiết: </b> {{ props.weather }}°C
			</p>
		</div>
	</div>
</template>
<style scoped>
.clock-wraper {
	display: flex;
}

.clock-wraper select {
	width: 50%;
	height: 30px;
	border: 1px solid gray;
	border-radius: 5px;
}

.clock {
	width: 70px;
	height: 70px;
	border-radius: 50%;
	background-color: #fff;
	position: relative;
	border: 1px solid black;
}

.select-wraper {
	display: flex;
	align-items: center;
	margin-left: 10px;
}

.clock::after {
	content: "";
	position: absolute;
	top: 50%;
	left: 50%;
	transform: translate(-50%, -50%);
	width: 5px;
	height: 5px;
	border-radius: 50%;
	background: #000;
}

.number {
	--rotation: 0deg;
	text-align: center;
	position: absolute;
	height: 100%;
	width: 100%;
	text-align: center;
	transform: rotate(var(--rotation));
	font-size: 8px;
}

.number1 {
	--rotation: 30deg;
}

.number2 {
	--rotation: 60deg;
}

.number3 {
	--rotation: 90deg;
}

.number4 {
	--rotation: 120deg;
}

.number5 {
	--rotation: 150deg;
}

.number6 {
	--rotation: 180deg;
}

.number7 {
	--rotation: 210deg;
}

.number8 {
	--rotation: 240deg;
}

.number9 {
	--rotation: 270deg;
}

.number10 {
	--rotation: 300deg;
}

.number11 {
	--rotation: 330deg;
}

.hand {
	--rotation: 0deg;
	position: absolute;
	bottom: 50%;
	left: 50%;
	background: #000;
	transform-origin: bottom;
	transform: translatex(-50%)rotate(var(--rotation));
	border-radius: 30px;

}

.hour {
	width: 2px;
	height: 30%;
}

.minute {
	width: 2px;
	height: 33%;
	background-color: lightcoral;
	--rotation: 0deg;
}

.second {
	width: 1px;
	height: 35%;
	background-color: orangered;
	--rotation: 0deg;
}
</style>
