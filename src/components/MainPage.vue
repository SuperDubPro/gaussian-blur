<template>
	<div class="page">
		<div class="left-column">
			<img alt="Vue logo" src="../assets/logo.png">
		</div>
		<div class="right-column">
			<canvas id="example-canvas"></canvas>
		</div>
	</div>
</template>

<script>
	export default {
		name: "MainPage",
		created() {
			/*
			const vertexShaderText = `
				attribute vec2 vertex;
				attribute vec2 texCoord;

				varying vec2 vTexCoord;

				void main() {
					gl_Position = vec4(vertex, 0.0, 1.0);
					vTexCoord = texCoord;
				}
			`;
			const fragmentShaderText = `
				const int MAX_KOEFF_SIZE = 32; //максимальный размер ядра (массива коэффициентов)

				uniform sampler2D texture; //размываемая текстура
				uniform int kSize; //размер ядра
				uniform float koeff[MAX_KOEFF_SIZE]; //коэффициенты
				uniform vec2 direction; //направление размытия с учетом радиуса размытия и aspect ratio, например (0.003, 0.0) - горизонтальное и (0.0, 0.002) - вертикальное

				varying vec2 vTexCoord; //текстурные координаты текущего фрагмента

				void main() {
					vec4 sum = vec4(0.0); //результирующий цвет

					vec2 startDir = -0.5*direction*float(kSize-1); //вычисляем начальную точку размытия
					for (int i=0; i<kSize; i++) //проходимся по всем коэффициентам
						sum += texture2D(texture, vTexCoord + startDir + direction*float(i)) * koeff[i]; //суммируем выборки

					gl_FragColor = sum;
				}
			`;
			 */

			const vertexShaderText = `
				attribute vec2 vertexPosition;

				void main(){
					gl_Position = vec4(vertexPosition, 0, 1);
				}
			`;
			const fragmentShaderText = `
				precision mediump float;

				void main(){
					gl_FragColor = vec4(0.0, 0.0, 1.0, 1.0);
				}
			`;

			let StartWebGL = function () {

				let canvas = document.getElementById('example-canvas');
				let gl = canvas.getContext('webgl');

				if (!gl) {
					alert('Your browser does not support WebGL');
					return;
				}

				canvas.height = gl.canvas.clientHeight;
				canvas.width = gl.canvas.clientWidth;


				gl.viewport(0,0,gl.canvas.width,gl.canvas.height);


				let vertexShader = gl.createShader(gl.VERTEX_SHADER);
				let fragmentShader = gl.createShader(gl.FRAGMENT_SHADER);

				gl.shaderSource(vertexShader, vertexShaderText);
				gl.shaderSource(fragmentShader, fragmentShaderText);

				gl.compileShader(vertexShader);
				if (!gl.getShaderParameter(vertexShader, gl.COMPILE_STATUS)) {
					alert('Error compiling shader!');
					console.error('Shader error info: ', gl.getShaderInfoLog(vertexShader));
				}
				gl.compileShader(fragmentShader);
				if (!gl.getShaderParameter(fragmentShader, gl.COMPILE_STATUS)) {
					alert('Error compiling shader!');
					console.error('Shader error info: ', gl.getShaderInfoLog(fragmentShader));
				}

				let program = gl.createProgram();
				gl.attachShader(program, vertexShader);
				gl.attachShader(program, fragmentShader);

				gl.linkProgram(program);
				gl.validateProgram(program);

				if (!gl.getProgramParameter(program, gl.VALIDATE_STATUS)) {
					console.error('Error validating program ', gl.getProgramInfoLog(program));

					return;
				}


				let vertexBuffer = gl.createBuffer();
				gl.bindBuffer(gl.ARRAY_BUFFER, vertexBuffer);

				let vertexArray = [
					//   X,   Y
					0.0, 0.5,
					0.5, -0.5,
					-0.5, -0.5
				];

				gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(vertexArray), gl.STATIC_DRAW);

				let positionAttribLocation = gl.getAttribLocation(program, 'vertexPosition');

				gl.vertexAttribPointer(
					positionAttribLocation, // ссылка на атрибут
					2, // кол-во элементов на 1 итерацию
					gl.FLOAT, // тип данных
					gl.FALSE, // нормализация
					2 * Float32Array.BYTES_PER_ELEMENT, // элементов массива на одну вершину
					0 * Float32Array.BYTES_PER_ELEMENT // отступ для каждой вершины
				);

				gl.enableVertexAttribArray(positionAttribLocation);

				gl.clearColor(0.75, 0.9, 1.0, 1.0);
				gl.clear(gl.COLOR_BUFFER_BIT);
				gl.useProgram(program);
				gl.drawArrays(gl.TRIANGLES, 0, 3);

			};


			document.addEventListener('DOMContentLoaded', function() {
				// InitWebGL();
				StartWebGL();
			});
		},
	}

</script>

<style scoped lang="scss">
	$vue-color1: #42b983;
	$vue-color2: #2c3e50;

	.page {
		display: flex;
		flex-direction: row;
		align-content: flex-start;
		width: 100%;
		height: 100%;
		margin: 0 100px 0 100px;
		.left-column {
			height: 200px;
			width: 30%;
			border: 2px solid $vue-color1;
		}
		.right-column {
			height: 100px;
			width: 30%;
			border: 2px solid $vue-color1;
		}
	}
</style>