<script lang="ts">
  import { spring } from "svelte/motion";

  let count = 0;

  const displayed_count = spring();
  $: displayed_count.set(count);
  // $: offset = modulo($displayed_count, 1);

  function modulo(n: number, m: number) {
    // handle negative numbers
    return ((n % m) + m) % m;
  }

  // la interfaz sirve para que no tengas que acordarte que propiedades tiene un objeto y el tipo que son
  interface diferenciaFechas {
    dias: number;
    horas: number;
    minutos: number;
    segundos: number;
  }

  let reloj: string = " ";

  setInterval(() => {
    const date: Date = new Date();
    let fecha: string = date.toLocaleDateString("es-ES", {
      weekday: "long",
      day: "2-digit",
      month: "2-digit",
      year: "numeric",
      hour: "2-digit",
      minute: "2-digit",
      second: "2-digit",
    });
    reloj = fecha;
  }, 1000);
  let intervalo: any;
  // se tiene que poder hacer algo con lo que devuelve la funcion diferencia fecha
  function startCountDown(diferenceDate: diferenciaFechas) {
    intervalo = setInterval(() => {
      imprimirContador = restarSegundo(diferenceDate);

      if (
        diferenceDate.dias === 0 &&
        diferenceDate.horas === 0 &&
        diferenceDate.minutos === 0 &&
        diferenceDate.segundos === 0
      ) {
        imprimirContador = { dias: 0, horas: 0, minutos: 0, segundos: 0 };
        clearInterval(intervalo);
      }

      console.log(diferenceDate);
    }, 1000);
  }

  function restarSegundo(diferenceDate: diferenciaFechas) {
    if (diferenceDate.segundos > 0) {
      diferenceDate.segundos--;
    } else {
      if (diferenceDate.minutos > 0) {
        diferenceDate.minutos--;
        diferenceDate.segundos = 59;
      } else {
        if (diferenceDate.horas > 0) {
          diferenceDate.horas--;
          diferenceDate.minutos = 59;
          diferenceDate.segundos = 59;
        } else {
          if (diferenceDate.dias > 0) {
            diferenceDate.dias--;
            diferenceDate.horas = 23;
            diferenceDate.minutos = 59;
            diferenceDate.segundos = 59;
          }
        }
      }
    }
    return diferenceDate;
  }

  let userInput = "";
  let diferencia: diferenciaFechas | null;
  let imprimirContador: diferenciaFechas = {
    dias: 0,
    horas: 0,
    minutos: 0,
    segundos: 0,
  };
  let validDate = false;
  function updateDate() {
    detenerCuentaRegresiva();
    if (userInput !== "") {
      let currentDate: Date = new Date();
      let userInputDate = new Date(userInput);
      if (userInputDate > currentDate) {
        diferencia = diferenciaFechas(currentDate, userInputDate);
        validDate = true;
        startCountDown(diferencia);
      } else {
        imprimirContador = {
          dias: 0,
          horas: 0,
          minutos: 0,
          segundos: 0,
        };
        validDate = false;
      }
    }
  }
  function detenerCuentaRegresiva(): void {
    clearInterval(intervalo);
  }
  // optimizar esta parte del codigo porque he hecho copia pega del chat gpt xddddd
  // en vez de imprimir los dias tendria que imprimir como mucho las horas
  function diferenciaFechas(fecha1: any, fecha2: any) {
    let diferencia = Math.abs(fecha1 - fecha2); // Obtiene la diferencia en milisegundos

    let dias = Math.floor(diferencia / (1000 * 60 * 60 * 24)); // Convierte milisegundos a días
    diferencia -= dias * (1000 * 60 * 60 * 24);

    let horas = Math.floor(diferencia / (1000 * 60 * 60)); // Convierte milisegundos a horas
    diferencia -= horas * (1000 * 60 * 60);

    let minutos = Math.floor(diferencia / (1000 * 60)); // Convierte milisegundos a minutos
    diferencia -= minutos * (1000 * 60);

    let segundos = Math.floor(diferencia / 1000); // Convierte milisegundos a segundos

    return {
      dias: dias,
      horas: horas,
      minutos: minutos,
      segundos: segundos,
    };
  }
</script>

<span>{reloj}</span>
<label>
  <input
    bind:value={userInput}
    on:change={updateDate}
    type="datetime-local"
    name="userDdate"
    id="userDdate"
  />
</label>
<p>La fecha es: {userInput}</p>
{#if diferencia != null && validDate}
  <p>
    Quedan {#if imprimirContador?.dias != 0}
      {imprimirContador?.dias} dias,{/if}
    {imprimirContador?.horas} horas, {imprimirContador?.minutos}
    minutos, {imprimirContador?.segundos} segundos
  </p>
{:else if diferencia != null && !validDate}
  <p>Introduce una fecha correcta. No podemos retroceder en el timpo.</p>
{:else}
  <p>
    Introduce una fecha para tener tu contador de <strong>DiGref</strong> 😎😎😎
  </p>
{/if}

<div class="counter">
  <!-- <button on:click={() => (count -= 1)} aria-label="Decrease the counter by one">
		<svg aria-hidden="true" viewBox="0 0 1 1">
			<path d="M0,0.5 L1,0.5" />
		</svg>
	</button> -->

  <div class="counter-viewport">
    <div class="counter-digits">
      <strong>{reloj}</strong>
    </div>
  </div>

  <!-- <button on:click={() => (count += 1)} aria-label="Increase the counter by one">
		<svg aria-hidden="true" viewBox="0 0 1 1">
			<path d="M0,0.5 L1,0.5 M0.5,0 L0.5,1" />
		</svg>
	</button> -->
</div>
<div class="counter">
  <!-- <button on:click={() => (count -= 1)} aria-label="Decrease the counter by one">
		<svg aria-hidden="true" viewBox="0 0 1 1">
			<path d="M0,0.5 L1,0.5" />
		</svg>
	</button> -->

  <div class="counter-viewport">
    <div class="counter-digits">
      <strong
        >{imprimirContador.dias}:{imprimirContador.horas}:{imprimirContador.minutos}:{imprimirContador.segundos}</strong
      >
    </div>
  </div>

  <!-- <button on:click={() => (count += 1)} aria-label="Increase the counter by one">
		<svg aria-hidden="true" viewBox="0 0 1 1">
			<path d="M0,0.5 L1,0.5 M0.5,0 L0.5,1" />
		</svg>
	</button> -->
</div>

<style>
  .counter {
    display: flex;
    border-top: 1px solid rgba(0, 0, 0, 0.1);
    border-bottom: 1px solid rgba(0, 0, 0, 0.1);
    margin: 1rem 0;
    width: 100%;
  }

  /* .counter button {
    width: 2em;
    padding: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    border: 0;
    background-color: transparent;
    touch-action: manipulation;
    font-size: 2rem;
  } */

  /* .counter button:hover {
    background-color: var(--color-bg-1);
  }

  svg {
    width: 25%;
    height: 25%;
  }

  path {
    vector-effect: non-scaling-stroke;
    stroke-width: 2px;
    stroke: #444;
  } */

  .counter-viewport {
    width: 100%;
    height: 4em;
    overflow: hidden;
    text-align: center;
    position: relative;
  }

  .counter-viewport strong {
    position: absolute;
    display: flex;
    width: 100%;
    height: 100%;
    font-weight: 400;
    color: var(--color-theme-1);
    font-size: 4rem;
    align-items: center;
    justify-content: center;
  }

  .counter-digits {
    position: absolute;
    width: 100%;
    height: 100%;
  }

  /* .hidden {
    top: -100%;
    user-select: none;
  } */
</style>
