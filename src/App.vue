<script setup>
  import {ref} from "vue"

  const day = ref("")
  const month = ref("")
  const year = ref("")
  const ageYears = ref(0)
  const ageMonths = ref(0)
  const ageDays = ref(0)
  const showResult = ref(false)
  const errors = ref({})
  const currentDate = new Date();

  const calculateAge = () => {
    const birthDate = new Date(year.value, month.value - 1, day.value);
  
    let yearsDiff = currentDate.getFullYear() - birthDate.getFullYear();
    let monthsDiff = currentDate.getMonth() - birthDate.getMonth();
    let daysDiff = currentDate.getDate() - birthDate.getDate();
  
    if (monthsDiff < 0 || (monthsDiff === 0 && daysDiff < 0)) {
      yearsDiff--;
      monthsDiff += 12;
    }
  
    if (daysDiff < 0) {
      const lastMonthDate = new Date(currentDate.getFullYear(), currentDate.getMonth(), 0);
      daysDiff = lastMonthDate.getDate() - birthDate.getDate() + currentDate.getDate();
      monthsDiff--;
    }
    showResult.value = true
    ageYears.value = yearsDiff;
    ageMonths.value = monthsDiff;
    ageDays.value = daysDiff;
  }

  const validateForm = () => {
    errors.value = {}

    if(!day.value) {
      errors.value.day = "This field is required"
    }else if(day.value<1 || day.value>31) {
      errors.value.day = "Must be a valid day"
    }

    if(!month.value) {
      errors.value.month = "This field is required"
    }else if(month.value<1 || month.value>12) {
      errors.value.month = "Must be a valid month"
    }

    if((month.value == 4 || month.value == 6 || month.value == 8 || month.value == 9 || month.value == 11) && (day.value>30)){
      errors.value.day = "That month doesn't count more than 30 days"
    }
    
    if((month.value == 2) && (day.value>29) && ((year.value % 4 === 0 && year.value % 100 !== 0) || (year.value % 400 === 0))) {
      errors.value.day = "Not a valid date for february in bissextile year"
    }else if((month.value == 2) && (day.value>28) && !((year.value % 4 === 0 && year.value % 100 !== 0) || (year.value % 400 === 0))) {
      errors.value.day = "Not a valid date for february in non bissextile year"
    }
    
    if(!year.value) {
      errors.value.year = "This field is required"
    }else if(year.value>currentDate.getFullYear()) {
      errors.value.year = "Must be in the past"
    }

    else{calculateAge()}
  }

</script>
<template>
  <main>
    <article class="age-calculator">
      <form id="form" class="form-container" @submit.prevent="validateForm()">
        <div class="input-wrapper">
          <label for="day" :class="{error : errors.day}">Day</label>
          <input v-model="day" id="day" type="text" placeholder="DD" :class="{error : errors.day}">
          <p v-if="errors.day" :class="{error : errors.day}">{{errors.day}}</p>
        </div>
        <div class="input-wrapper">
          <label for="month" :class="{error : errors.month}">Month</label>
          <input v-model="month" id="month" type="text" placeholder="MM" :class="{error : errors.month}">
          <p v-if="errors.month" :class="{error : errors.month}">{{errors.month}}</p>
        </div>
        <div class="input-wrapper">
          <label for="year" :class="{error : errors.year}">Year</label>
          <input v-model="year" id="year" type="text" placeholder="YYYY" :class="{error : errors.year}">
          <p v-if="errors.year" :class="{error : errors.year}">{{errors.year}}</p>
        </div>
      </form>
      <div class="icon-group">
        <button form="form" @click="validateForm()"><svg xmlns="http://www.w3.org/2000/svg" width="23" height="22" viewBox="0 0 46 44"><g fill="none" stroke="#FFF" stroke-width="2"><path d="M1 22.019C8.333 21.686 23 25.616 23 44M23 44V0M45 22.019C37.667 21.686 23 25.616 23 44" @click="show()"/></g></svg></button>
        <hr>
      </div>
      <!-- <Transition name="results">
        <div class="p-group">
          <p class="text"><span>{{ showResult ? ageYears : "--"}}</span> years</p>
          <p><span>{{ showResult ? ageMonths : "--" }}</span> months</p>
          <p><span>{{ showResult ? ageDays : "--"}}</span> days</p>
        </div>
      </Transition> -->
      <Transition name="results">
        <div class="p-group" v-if="showResult" key="result">
          <p class="text"><span>{{ ageYears }}</span> years</p>
          <p><span>{{ ageMonths }}</span> months</p>
          <p><span>{{ ageDays }}</span> days</p>
        </div>
        <div class="p-group" v-else key="placeholder">
          <p class="text"><span>--</span> years</p>
          <p><span>--</span> months</p>
          <p><span>--</span> days</p>
        </div>
      </Transition>
    </article>
  </main>
</template>

<style scoped>
  article {
    background-color: var(--clr-neutral-0);
    padding: 4rem 1.5rem;
    border-radius: 1.5rem 1.5rem 6rem 1.5rem;
    min-width: 21.5rem;
    max-width: 52.5rem;
    margin: 5.5rem 1rem;
  }
  .form-container {
    display: flex;
    justify-content: space-between;
    margin-bottom: 4.5rem;
    max-width: 33rem;
  }
  .input-wrapper {
    display: flex;
    flex-direction: column;
    min-width: 20%;
    max-width: 30%;
  }

  label {
    font-size: 0.75rem;
    text-transform: uppercase;
    letter-spacing: 2px;
    color: var(--clr-neutral-500);
    margin-bottom: 0.125rem;
  }

  input {
    border-radius: 10px;
    border: solid 1px var(--clr-neutral-200);
    font-size: 1rem;
    padding: 0.75rem;
  }

  input:focus {
    border: solid 1px var(--clr-primary-400);
    outline: none
  }

  .icon-group {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
  
  button {
    background-color: var(--clr-primary-400);
    padding: 1.125rem;
    border-radius: 50%;
    z-index: 2;
    position: absolute;
    border: none;
  }

  button:hover {
    background-color: black;
  }

  hr{
    border: 1px solid var(--clr-neutral-100);
    min-width: 100%;
    z-index: 1;
  }

  .p-group {
    margin-top: 4.5rem;
  }

  .p-group p, .p-group span {
    font-weight: var(--fw-xbold-800);
    font-style: italic;
    font-size: 3.5rem;
    line-height: 3.5rem;
  }

  span {
    color: var(--clr-primary-400);
  }

  p.error  {
    color: var(--clr-secundary-300);
    font-weight: var(--fw-reg-400);
    font-style: italic;
    font-size: 0.875rem;
    margin-top: 0.5rem;
  }

  input.error {
    border: 1px solid var(--clr-secundary-300);
  }

  label.error  {
    color: var(--clr-secundary-300);
  }

  .results-enter-from {
    opacity: 0;
  }
  .results-enter-to {
    opacity: 1;
  }
  .results-enter-active {
    transition: all 2.5s ease;
  }

  @media (min-width: 700px) {

    article {
      padding: 3.875rem;
      margin: 11rem auto;
    }

    .p-group p, .p-group span {
      font-size: 5rem;
      line-height: 5rem;
    }

    input {
      padding: 1rem 1.5rem;
      font-size: 1.5rem;
    }

    button {
      padding: 2rem;
    }

    .icon-group {
    align-items: end;
    }

    svg {
      width: 44px;
      height: 46px;
    }

    label {
      margin-bottom: 0.5rem;
      font-size: 0.875rem;
    }
  }
  
</style>
