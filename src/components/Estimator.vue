<script setup lang="ts">
import { ref } from 'vue'
import { DateTime } from 'luxon'

let postition = ref(0)
let date = ref("now")

const seconds = [
    60 * 60, // 1 hour
    60 * 60 * 2, // 2 hours
    60 * 60 * 4, // 4 hours
    60 * 60 * 8, // 1 working day
    60 * 60 * 16, // 2 working days
    60 * 60 * 8 * 3.5, // 3.5 working days
    60 * 60 * 40, // 1 working week
]
const showOverflow = ref(false)
const showResultScreen = ref(false)
function shiftPosition() {

    if (postition.value < seconds.length - 1) {
        postition.value++

        var newDate = DateTime.local()
        newDate.plus({ seconds: seconds[postition.value] })

        // add the time between 5pm and 9am for each full working day
        let days = Math.floor(seconds[postition.value] / (60 * 60 * 8))

        // add the time between 5pm and 9am if the target time is after 5pm
        if (newDate.hour >= 17) {
            days++
        }

        date.value = DateTime.local().plus({ hours: 16 * days, seconds: seconds[postition.value] }).toLocaleString(DateTime.DATETIME_MED_WITH_WEEKDAY)
    } else {
        showOverflow.value = true
    }
}

function showResult() {
    showResultScreen.value = true
}

shiftPosition()
</script>

<template>
    <div class="m-4">
        <div class="estimates" v-if="!showResultScreen">

            <div class="estimate" v-if="!showOverflow">
                <h1>If I start with the task <b>right now</b> I can deliver it by <b>{{ date }}</b>.</h1>
                <button @click="showResult()" class="p-2 bg-green-300 rounded-xl mr-4">Yes</button>
                <button @click="shiftPosition()" class="p-2 bg-red-700 text-white rounded-xl">No</button>
                <h2>Assumptions:</h2>
                <ul>
                    <li>I work 8 hours a day</li>
                    <li>I work between 8am and 5pm</li>
                    <li>I work 7 days a week</li>
                </ul>
            </div>
            <div class="no-estimate" v-if="showOverflow">
                <h1>
                    Tell your manager that the "task" needs to be broken up into smaller pieces</h1>
            </div>
        </div>
        <div class="result" v-if="showResultScreen">
            <h1>Your estimate is <b>{{ Math.ceil( (seconds[postition] / 60 / 60) * 1.25) }} hours</b>.</h1>
        </div>
    </div>
</template>