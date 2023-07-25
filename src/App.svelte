<script>
  import { onMount } from 'svelte';

  let shifts = [{}];
  let wage = null;
  let taxRate = null;

  let totalHours = 0;
  let totalPay = 0;
  let afterTax = 0;

	let currency;

  function addShift() {
    shifts = [...shifts, {}];
  }

  function removeShift(i) {
    shifts.splice(i, 1);
    shifts = shifts;
  }

// state variable to track blur
let blurred = false;  

$: {

  // original totalHours logic
  
  totalHours = shifts.reduce((sum, shift) => {
    return sum + parseFloat(shift.hours) || 0;
  }, 0);

  // format after reducing
  if (blurred) {
    shifts.forEach(formatShiftHours);
    blurred = false; 
  }

  // other totals
  totalPay = totalHours * wage;

  afterTax = totalPay - (totalPay * (taxRate / 100)); 

}

// on blur handler
function handleBlur() {
  blurred = true; 
}


function formatShiftHours(shift) {

  let original = shift.hours;

  if(typeof shift.hours === 'string' && shift.hours.includes(':')) {

    try {
      let [hours, mins] = shift.hours.split(':');
      let decimalHours = parseFloat(hours) + parseFloat(mins) / 60;

      if(!isNaN(decimalHours)) {
        shift.hours = decimalHours;
      }

    } catch {
      shift.hours = original; 
    }

  }

}

</script>

<header>
	<h1>TimeCalc 2.0</h1>
	<p>Calc-You-Later!</p>
</header>

<main class="app">
	
  <div class="inputs">
		
	    {#each shifts as shift, i}
	      <div class="single-shift">
	        <input on:blur={handleBlur} type="text" bind:value={shift.hours} placeholder="hours worked" />
	        <button class="remove-single-shift" on:click={() => removeShift(i)}>&#8211;&#8211;</button>
	      </div>
	    {/each}
	
	    <button on:click={addShift}>Add Shift</button>
		
			<div class="row-hourly_wage">
		    <input type="number" min="0" bind:value={wage} placeholder="hourly wage" />
		    <select class="select-currency_symbol" bind:value={currency}>
		      <option value="&#36;">$</option>
					<option value="&#8364;">&#8364;</option>
					<option value="&#165;">&#165;</option>
					<option value="&#163;">&#163;</option>
					<option value="&#8377;">&#8377;</option>
					<option value="&#8381;">&#8381;</option>
					<option value="&#8378;">&#8378;</option>
					<option value="&#8361;">&#8361;</option>
					<option value="&#3647;">&#3647;</option>
					<option value="&#8372;">&#8372;</option>
					<option value="&#8355;">Fr.</option> 
					<option value="&#8355;">fr.</option>
					<option value="&#82;">R</option>
					<option value="&#107;&#114;">kr</option>
					<option value="&#107;&#114;">Nkr</option> 
					<option value="&#82;&#112;">Rp</option>
		    </select>
			</div>

	    <input type="number" min="0" bind:value={taxRate} placeholder="taxes & fees (%)"/>

  </div>

  <div class="outputs">
    <div>
      <h4>Total Hours</h4>
      <p>{totalHours.toFixed(2)}</p>
    </div>

    <div>
      <h4>Total Pay</h4>
      <p>{currency}{totalPay.toFixed(2)}</p> 
    </div>

    <div>
      <h4>After Tax</h4>
      <p>{currency}{afterTax.toFixed(2)}</p>
    </div>
  </div>
	
</main>

<footer>made with love by Eli <em>@permafriday</em></footer>

<style>
	* {
	 /* border: 1px solid red !important; */
		font-family: 'Open Sans', system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', sans-serif;
		margin: 0;
		padding: 0;
	}

	header {
		text-align: center;
		margin-bottom: 1rem;
	}
	
  .app {
    max-width: 333px;
    margin: 0 auto;
    background: #334155;
    color: #fff;
    padding: 20px;
		border-radius: 10px;
  }

	.inputs {
		display: flex;
		flex-direction: column;
	}

  input, button {
		font-size: clamp(0.6rem, 4vw, 1.18rem);
    border: none;
		margin: 5px;
		padding: 0.5rem 0.25rem;
    border-radius: 5px;
		/* text-align: center; */
  }

	input {
		outline: hsl(0, 0%, 40%) 1px solid;
	}
	
  button {
    background: #475569;
    color: #eee;
    cursor: pointer;
		transition: transform 0.2s;
  }
	

	button:hover {
	  animation: pulse 0.75s;
	}

	@keyframes pulse {
	  0% { transform: scale(1); }
	  50% { transform: scale(1.075); }
	  100% { transform: scale(1); }
	}

	select {
		border-radius: 5px;
		border: none;
		outline: hsl(0, 0%, 40%) 1px solid;
		transition: transform 0.2s;
	}

	.single-shift button {
		background: lightcoral;
	}

	button.remove-single-shift {
		font-weight: 600;
	}

	.select-currency_symbol {
		margin: 5px;
		font-size: 1.2rem;
	}

	.single-shift, .row-hourly_wage {
		display: grid;
		grid-template-columns: 2fr 1fr;
	}
	
  .outputs {
    margin-top: 30px;
    display: flex;
    justify-content: space-between;
  }

	.outputs div {
		outline: 3px solid white;
		border-radius: 10px;
		padding: 0.5rem;
	}

  .outputs div {
    text-align: center;
  }

	footer {
		position: absolute;
		bottom: 6px;
		left: 9px;
	}

	@media (max-width: 369px) {
		
  .outputs {
		margin-top: 60px;
    flex-direction: column;
		gap: 30px;
  }
}


	@media (max-width: 300px) {
		
  .single-shift, .row-hourly_wage {
		display: flex;
		flex-direction: column;
	}

	input {
		/* text-align: center; */
	}

	.inputs {
		display: flex;
		flex-direction: column;
	}

  .app {
    /* padding: 10px 0; */
  }
	

</style>