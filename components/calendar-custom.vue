<script setup lang="ts">
import { ref, computed } from 'vue';
import DatePicker from "primevue/datepicker";

/**
 * Reactive Variables
 *
 * @const mode {Ref<'single' | 'range'>} - Defines the date selection mode:
 *    - 'single': Allows the user to select a single date.
 *    - 'range': Allows the user to select a range of dates.
 *    Using `mode` provides a more intuitive interface for the user, as the calendar dynamically adapts based on the selection mode.
 *
 * @const dates {Ref<Date | [Date | null, Date | null] | null>} - Holds the selected date(s):
 *    - `Date`: Used when `mode` is set to 'single' for single-date selection.
 *    - `[Date | null, Date | null]`: Array for range selection, where:
 *        - dates[0] represents the start date
 *        - dates[1] represents the end date.
 *    - `null`: Initialized as null to represent no selection by default.
 *
 * @const minDate {Ref<Date>} - Sets the minimum selectable date, initialized to the current date.
 *    Though not a strict requirement, this can be useful for restricting date selection to future dates in specific use cases.
 */
const mode = ref<'single' | 'range'>('single');
const dates = ref<Date | [Date | null, Date | null] | null>(null);
const minDate = ref(new Date());

// Computed para calcular el número de días entre start y end
const daysBetween = computed(() => {
    if (Array.isArray(dates.value) && dates.value[0] && dates.value[1]) {
        const diffTime = Math.abs(dates.value[1]!.getTime() - dates.value[0]!.getTime());
        return Math.ceil(diffTime / (1000 * 60 * 60 * 24)) + 1;
    }
    return 1;
});

/**
 * @function incrementEndDate
 * Increments the end date in mode range dates.
 */
function incrementEndDate() {
    if (Array.isArray(dates.value) && dates.value[0] && dates.value[1]) {
        const newEnd = new Date(dates.value[1] || dates.value[0]);
        newEnd.setDate(newEnd.getDate() + 1);
        dates.value[1] = newEnd;
    }
}

/**
 * @function decrementEndDate
 * Decrement the end date in mode range dates.
 */
function decrementEndDate() {
    if (Array.isArray(dates.value) && dates.value[1] && daysBetween.value > 1) {
        const newEnd = new Date(dates.value[1]);
        newEnd.setDate(newEnd.getDate() - 1);
        dates.value[1] = newEnd;
    }
}

/**
 * @function changeMode
 * Changes the selection mode between 'single' and 'range'.
 * - Resets `dates` to `null` in 'single' mode to remove any range selection.
 * @param {string} newMode - The new selection mode ('single' or 'range').
 */
function changeMode(newMode) {
    mode.value = newMode;
    if(newMode === 'single') {
        dates.value = null;
    } else {
        dates.value = [];
    }
}

/**
 * @function clearDates
 * Clears the selected dates, resetting `dates` to `null`.
 */
function clearDates() {
    dates.value = null;
}

/**
 * @function formatDate
 * Formats a given Date object to the format 'dd/mm/yyyy'.
 * @param {Date | null} date - The date to format. Returns an empty string if `date` is null.
 * @returns {string} - The formatted date string.
 */
function formatDate(date: Date | null): string {
    if (!date) return '';
    const day = String(date.getDate()).padStart(2, '0');
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const year = date.getFullYear();
    return `${day}/${month}/${year}`;
}

/**
 * @computed formattedDates
 * Computes the display format of the selected dates.
 * - For 'single' mode: Returns the single selected date twice (start and end are the same).
 * - For 'range' mode: Returns a formatted range (start and end date).
 * - Returns an empty string if there are no valid dates.
 */
const formattedDates = computed(() => {
    if (mode.value === 'single' && dates.value) {
        return `${formatDate(dates.value)}-${formatDate(dates.value)}`;
    } else if (Array.isArray(dates.value) && dates.value[0] && dates.value[1]) {
        return `${formatDate(dates.value[0])}-${formatDate(dates.value[1])}`;
    }
    return '';
});
</script>

<template>
    <div class="date-picker-wrapper">
        <div class="mode-selector">
            <label>
                <input type="radio" value="single" v-model="mode" @change="changeMode('single')" />
                Single
            </label>
            <label>
                <input type="radio" value="range" v-model="mode" @change="changeMode('range')" />
                Range
            </label>
        </div>

        <div class="date-info">
            <div class="days-selected">
                N° days:
                <strong v-show="mode === 'range' && dates !== null">
                    {{ daysBetween }}
                </strong>
                <strong v-show="mode === 'single'">
                    1
                </strong>
            </div>
            <label>Dates:</label>
            <input type="text" :value="formattedDates" readonly />
        </div>

        <div class="date-info">
            <DatePicker
                v-model="dates"
                inline
                class="w-full"
                :minDate="minDate"
                dateFormat="dd/mm/yy"
                placeholder="Selecciona la fecha"
                :selectionMode="mode === 'single' ? 'single' : 'range'"
                :manualInput="false"
            />
        </div>

        <div v-if="mode === 'range' && dates !== null" class="button-group">
            <Button label="Decrease" icon="pi pi-minus" @click="decrementEndDate" class="button-decrement" />
            <Button label="Clean" severity="danger" icon="pi pi-trash" @click="clearDates" class="button-clean" />
            <Button label="Increase" icon="pi pi-plus" @click="incrementEndDate" class="button-increment" />
        </div>
    </div>
</template>

<style scoped>
.date-picker-wrapper {
    padding: 20px;
    background-color: #1e1c24;
    border-radius: 8px;
    box-shadow: 0px 4px 12px rgba(0, 0, 0, 0.4);
    max-width: 400px;
    margin: 20px auto;
}

.mode-selector {
    display: flex;
    gap: 10px;
    justify-content: center;
    margin-bottom: 20px;
}

.mode-selector label {
    color: #fff;
    font-weight: 500;
    cursor: pointer;
}

.date-info {
    margin-bottom: 20px;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 5px;
}

.date-info label {
    color: #9a9a9a;
    font-size: 0.9em;
}

.date-info input[type="text"] {
    width: 100%;
    padding: 10px;
    border-radius: 6px;
    border: 1px solid #454352;
    background-color: #2a2833;
    color: #e0e0e0;
    text-align: center;
    font-weight: 500;
}

.days-selected {
    color: #a0a0a0;
    font-size: 0.85em;
    text-align: center;
    margin-top: 5px;
}

.button-group {
    display: flex;
    justify-content: space-between;
    margin-top: 10px;
}

.button-group .p-button {
    width: 100px;
    margin: 5px;
    border-radius: 6px;
    background-color: #353340;
    color: #e0e0e0;
}

.button-decrement,
.button-increment {
    background-color: #4caf50;
    color: #fff;
}

.button-clean {
    background-color: #e53935;
    color: #fff;
}
</style>
