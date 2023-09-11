<script>
	import * as d3 from 'd3';
	import { onMount } from 'svelte';
	import axios from 'axios';

	let selectedOption = 'Other Coders';

	/*
  let data = [
    { etiqueta: 'Python', valor: 3314 },
    { etiqueta: 'JavaScript', valor: 2785 },
    { etiqueta: 'Bash/Shell (todos los shells)', valor: 2614 },
  ];
*/
	let data = [];
	async function fetchData() {
		const VITE_BACKEND_URL = 'http://127.0.0.1:3000';
		try {
			const servidorAxios = axios.create({
				baseURL: `${VITE_BACKEND_URL}/api/v1`
			});

			const response = await servidorAxios({
				method: 'GET',
				url: `/others`,
				withCredentials: true
			});
			const others = response.data?.data?.data;
			// Simulamos la obtención de datos de una API

			// Calculamos los porcentajes
			//data = data.map(item => ({ ...item, valor: (item.valor / d3.sum(data, d => d.valor)) * 100 }));

			data = others.map((item) => {
				item.valor = item.valor.toString().replace('%', '') * 1;
				return item;
			});
			createChart(); // Llama a la función para crear el gráfico
		} catch (error) {
			data = [
				{ etiqueta: 'Python', valor: 63.61 },
				{ etiqueta: 'JavaScript', valor: 52.97 },
				{ etiqueta: 'Bash/Shell (todos los shells)', valor: 49.28 }
			];
		}
	}

	function seleccionarOpción(opción) {
		selectedOption = opción;
	}

	onMount(() => {
		fetchData(); // Llama a fetchData después de que la página se haya montado en el cliente
	});

	function createChart() {
		// Ancho del gráfico (ajustado a un valor más grande)
		const chartWidth = 750;

		// Selecciona el elemento con el id 'chart' y crea un elemento SVG para el gráfico
		const svg = d3
			.select('#chart')
			.append('svg')
			.attr('width', chartWidth) // Ancho del gráfico ajustado
			.attr('height', 3000); // Alto del gráfico

		// Escala lineal para mapear los valores a la anchura del gráfico
		const xScale = d3
			.scaleLinear()
			.domain([0, d3.max(data, (d) => d.valor)]) // Rango de valores de datos
			.range([0, chartWidth - 260]); // Rango de píxeles en el gráfico (ajustado)

		// Contenedor principal de las columnas
		const columnContainer = svg
			.selectAll('.column-container')
			.data(data)
			.enter()
			.append('g')
			.attr('class', 'contenedor-columna')
			.attr('transform', (d, i) => `translate(0, ${i * 50})`); // Espaciado vertical

		// Columna de etiquetas
		columnContainer
			.append('text')
			.attr('class', 'label-column')
			.attr('x', 160) // Espacio a la izquierda de las etiquetas (ajustado)
			.attr('y', 24) // Posición Y del texto
			.style('text-anchor', 'end')
			.text((d) => d.etiqueta)
			.attr('fill', '#DBDEE0'); // Color del texto

		// Columna de gráficos
		columnContainer
			.append('rect')
			.attr('x', 180) // Espacio a la izquierda de las gráficas (ajustado)
			.attr('width', (d) => xScale(d.valor)) // Ancho de la barra basado en el valor
			.attr('height', 30) // Altura fija de las barras
			.attr('fill', '#FF5353') // Color de relleno rojo
			.attr('stroke', '#4D5E63')
			.attr('stroke-width', 3)
			.attr('rx', 4)
			.attr('ry', 4);

		// Columna de valores en porcentaje
		columnContainer
			.append('text')
			.attr('class', 'value-column')
			.attr('x', (d) => xScale(d.valor) + 200) // Espacio a la izquierda de los valores (ajustado)
			.attr('y', 25) // Posición Y del texto
			.text((d) => `${d.valor.toFixed(2)}%`) // Mostramos el valor en porcentaje
			.attr('fill', '#DBDEE0');
	}
</script>

<nav>
	<a href="/" class="{selectedOption === 'All Respondents' ? 'seleccionado' : ''} nav-item-1">
		All Respondents
	</a>
	<a
		href="/professionaldev"
		class="{selectedOption === 'Professional Developers' ? 'seleccionado' : ''} nav-item-2"
	>
		Professional Developers
	</a>
	<a
		href="/learningtocode"
		class="{selectedOption === 'Learning to Code' ? 'seleccionado' : ''} nav-item-3"
	>
		Learning to Code
	</a>
	<a
		href="/othercoders"
		class="{selectedOption === 'Other Coders' ? 'seleccionado' : ''} nav-item-4"
	>
		Other Coders
	</a>
</nav>

<div id="chart" />

<style>
	#chart {
		display: flex;
		justify-content: center;
		align-items: center;
		height: 100%;
		width: 100%; /* Aprovecha todo el ancho disponible */
		background-color: #273d44;
		padding: 20px;
		box-sizing: border-box; /* Incluye el relleno en el cálculo del ancho */
	}

	:global(html, body) {
		background-color: #273d44;
		font-family: 'Roboto', sans-serif;
		color: white;
		margin: 0;
		padding: 0;
		margin-left: 20px;
	}

	/* Estilos para el menú de navegación */
	nav {
		display: flex;
		justify-content: flex-start;
		background-color: #273d44;
		padding: 10px;
		padding-top: 20px; /* Aumenta el espacio superior para que sea más alto */
		padding-bottom: 20px; /* Aumenta el espacio inferior para que sea más alto */
		border-radius: 30px;
		overflow: hidden;
		margin-left: 100px; /* Ajusta el margen izquierdo */
		width: 85%; /* Ajusta el ancho para abarcar más de la mitad horizontalmente */
	}

	.nav-item-1 {
		text-align: center;
		cursor: pointer;
		color: #B1B7BB;
		padding: 9px 1px;
		border-radius: 20px;
		transition: background-color 0.3s ease-in-out;
		text-decoration: none;
		margin-right: 5px;
		width: 14%;
	}
	.nav-item-2 {
		text-align: center;
		cursor: pointer;
		color: #B1B7BB;
		padding: 9px 1px;
		border-radius: 20px;
		transition: background-color 0.3s ease-in-out;
		text-decoration: none;
		margin-right: 2px;
		width: 16.5%;
	}
	.nav-item-3 {
		text-align: center;
		cursor: pointer;
		color: #B1B7BB;
		padding: 9px 1px;
		border-radius: 20px;
		transition: background-color 0.3s ease-in-out;
		text-decoration: none;
		margin-right: 2px;
		width: 13.5%;
	}
	.nav-item-4 {
		text-align: center;
		cursor: pointer;
		color: #FFFFFF;
		padding: 9px 1px;
		border-radius: 20px;
		transition: background-color 0.3s ease-in-out;
		text-decoration: none;
		margin-right: 2px;
		width: 12%;
	}

	.nav-item-1:hover {
		background-color: #3c5668;
	}
	.nav-item-2:hover {
		background-color: #3c5668;
	}
	.nav-item-3:hover {
		background-color: #3c5668;
	}
	.nav-item-4:hover {
		background-color: #ff5353;
	}

	.seleccionado {
		background-color: #ff5353;
	}
</style>
