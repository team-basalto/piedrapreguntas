<script>
    let themes = ['Geodiversidad', 'Geoparques', 'Geopatrimonio'];
    let selectedTheme = null;
    let currentQuestion = null;
    let questions = [];
    let spinning = false;
    let started = false;
    let rotationDegree = 0;
    let answerSelected = false;
    let isCorrect = false;
    let correctAnswer = '';
    let timer; // Variable para el temporizador
    let timeLeft = 20; // Tiempo restante

    // Función para girar la ruleta y seleccionar un tema
    function spinWheel() {
        if (!spinning) {
            spinning = true;
            rotationDegree = Math.floor(Math.random() * 360) + 720; // Gira al menos 2 vueltas
            const ruleta = document.getElementById('ruleta');
            ruleta.style.transform = `rotate(${rotationDegree}deg)`;

            setTimeout(() => {
                const segmentos = document.querySelectorAll('.segmento');
                const index = Math.floor((rotationDegree % 360) / 120); // Cambia a 120 para 3 segmentos
                selectedTheme = segmentos[index].dataset.value; // Obtener el tema seleccionado
                loadQuestions(selectedTheme);
            }, 4000); // Espera a que termine la animación
        }
    }

    // Función para cargar las preguntas basadas en el tema desde los archivos JSON
    async function loadQuestions(theme) {
        let response;
        switch (theme) {
            case 'Geodiversidad':
                response = await fetch('/preguntas/geodiversidad_questions.json');
                break;
            case 'Geoparques':
                response = await fetch('/preguntas/geoparques_questions.json');
                break;
            case 'Geopatrimonio':
                response = await fetch('/preguntas/geopatrimonio_questions.json');
                break;
            default:
                return;
        }

        if (response.ok) {
            const data = await response.json();
            questions = data;
            currentQuestion = questions[Math.floor(Math.random() * questions.length)];
            correctAnswer = currentQuestion.correct_answer;

            // Iniciar el temporizador de 20 segundos
            startTimer();
        } else {
            console.error('Error al cargar las preguntas');
        }
    }

    // Función para iniciar el temporizador
    function startTimer() {
        timeLeft = 20; // Reiniciar el tiempo
        timer = setInterval(() => {
            if (timeLeft <= 0) {
                clearInterval(timer);
                alert('¡Tiempo agotado! Has perdido.');
                restartGame();
            } else {
                timeLeft--; // Decrementar el tiempo
            }
        }, 1000);
    }

    // Función para manejar la selección de la respuesta
    function selectAnswer(option) {
        clearInterval(timer); // Detener el temporizador al seleccionar una respuesta
        if (!answerSelected) {
            answerSelected = true;
            const buttons = document.querySelectorAll('.answer-button');
            const body = document.body;

            // Cambiar color de fondo en base a la respuesta seleccionada
            body.style.transition = 'background-color 0.5s ease';
            if (option === correctAnswer) {
                isCorrect = true;
                body.style.backgroundColor = '#8BC34A'; // Verde pastel
            } else {
                body.style.backgroundColor = '#FFCDD2'; // Rojo pastel
            }

            // Resaltar las opciones
            buttons.forEach(button => {
                if (button.dataset.answer === correctAnswer) {
                    button.style.backgroundColor = '#8BC34A'; // Correcta
                }
                if (button.dataset.answer === option && option !== correctAnswer) {
                    button.style.backgroundColor = '#FFCDD2'; // Incorrecta
                }
                button.style.color = 'white';
            });

            // Mostrar el botón de reinicio
            const restartButton = document.querySelector('.restart-button');
            restartButton.style.display = 'block';
        }
    }

    // Función para reiniciar el juego
    function restartGame() {
        selectedTheme = null;
        currentQuestion = null;
        spinning = false;
        answerSelected = false;
        isCorrect = false;
        correctAnswer = '';
        clearInterval(timer); // Limpiar el temporizador
        const buttons = document.querySelectorAll('.answer-button');
        buttons.forEach(button => {
            button.style.backgroundColor = '#4CAF50'; // Restaurar color por defecto
            button.style.color = 'white';
        });

        // Ocultar el botón de reinicio
        const restartButton = document.querySelector('.restart-button');
        restartButton.style.display = 'none';

        // Reiniciar color de fondo
        document.body.style.backgroundColor = '';
    }
    function restart() {
        selectedTheme = null;
        currentQuestion = null;
        questions = [];
        spinning = false;
        started = false; // Reiniciar el estado de inicio
        answerSelected = false;
        isCorrect = false;
        correctAnswer = '';
        clearInterval(timer); // Limpiar el temporizador
        timer = false; // Ocultar el temporizador

        // Restaurar el color de fondo
        document.body.style.backgroundColor = '';
        
        // Ocultar el botón de reinicio
        const restartButton = document.querySelector('.restart-button');
        restartButton.style.display = 'none';
        
        // Reiniciar los botones de respuesta
        const buttons = document.querySelectorAll('.answer-button');
        buttons.forEach(button => {
            button.style.backgroundColor = '#4CAF50'; // Restaurar color por defecto
            button.style.color = 'white';
        });
    }

</script>

<style>
    body {
        margin: 0;
        font-family: 'Arial', sans-serif;
        background-color: #f4f4f4;
        transition: background-color 0.5s ease;
    }

    .container {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        height: 100vh;
        text-align: center;
        position: relative; /* Para posicionar el botón de regreso */
    }

    .title {
        font-size: 5rem;
        font-weight: bold;
        text-transform: uppercase;
        background: linear-gradient(90deg, #FF5722, #4CAF50);
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        margin-bottom: 50px;
        animation: pulse 2s infinite;
    }

    @keyframes pulse {
        0% { transform: scale(1); }
        50% { transform: scale(1.05); }
        100% { transform: scale(1); }
    }

    .ruleta {
        width: 300px;
        height: 300px;
        border-radius: 50%;
        border: 10px solid #333;
        position: relative;
        overflow: hidden;
        transition: transform 4s ease-out; /* Duración del giro */
        background-color: #62b3d3;
    }

    .segmento {
        position: absolute;
        width: 50%;
        height: 50%;
        background-color: #2edb56;
        border: 1px solid #000000;
        transform-origin: 100% 100%;
        display: flex;
        justify-content: center;
        align-items: center;
        font-weight: bold;
        color: #333;
    }

    .segmento:nth-child(1) { transform: rotate(0deg); }
    .segmento:nth-child(2) { transform: rotate(120deg); }
    .segmento:nth-child(3) { transform: rotate(240deg); }

    .pointer {
        position: absolute;
        top: -30px; /* Ajusta este valor para posicionar el puntero sobre la ruleta */
        left: 50%;
        transform: translateX(-50%);
        width: 0;
        height: 0;
        border-left: 10px solid transparent;
        border-right: 10px solid transparent;
        border-bottom: 20px solid red; /* Color del puntero */
        z-index: 10; /* Asegúrate de que esté por encima de la ruleta */
    }

    .timer {
        position: absolute;
        top: 20px;
        right: 20px;
        font-size: 1rem; /* Aumentar el tamaño de la fuente */
        color: rgb(0, 0, 0); /* Color del texto */
        background-color: rgba(124, 121, 121, 0.7); /* Fondo semi-transparente */
        padding: 10px 15px; /* Espaciado alrededor del texto */
        border-radius: 10px; /* Bordes redondeados */
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2); /* Sombra para un efecto elevado */
    }

    .answer-button {
        background-color: skyblue;
        color: white;
        padding: 15px;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        width: 100%;
        text-align: left;
        font-size: 1.2rem;
        margin-bottom: 10px;
        transition: background-color 0.3s ease;
    }

    .answer-button:hover {
        background-color: #45a049;
    }

    .restart-button {
        background-color: #FF5722;
        color: white;
        font-size: 1.2rem;
        padding: 10px 20px;
        border: none;
        border-radius: 20px;
        cursor: pointer;
        display: none;
        transition: background-color 0.3s ease;
    }

    .restart-button:hover, .back-button:hover {
        background-color: #e64a19;
    }

    .question-card {
        background-color: white;
        border-radius: 8px;
        padding: 20px;
        box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        margin-top: 20px;
        width: 300px;
    }

    .question-card p {
        font-size: 1.2rem;
        margin-bottom: 20px;
    }

    .back-button {
        position: absolute;
        bottom: 20px;
        left: 20px;
        background-color: #FF5722;
        color: white;
        font-size: 1.2rem;
        padding: 10px 20px;
        border: none;
        border-radius: 20px;
        cursor: pointer;
    }
</style>

<div class="container">
    <button class="back-button" on:click={restart}>Volver</button>
    {#if !started}
        <div class="title">GEOQUIZ</div>
        <button class="a-button" style="background-color: #4CAF50; color: white; padding: 15px 30px; border: none; border-radius: 5px; font-size: 1.5rem; cursor: pointer;" on:click={() => { started = true; }}>Comenzar</button>
    {/if}

    {#if started && !selectedTheme}
        <div class="ruleta" on:click={spinWheel} id="ruleta">
            <div class="segmento" data-value="Geodiversidad">Geodiversidad</div>
            <div class="segmento" data-value="Geoparques">Geoparques</div>
            <div class="segmento" data-value="Geopatrimonio">Geopatrimonio</div>
            <div class="pointer"></div> <!-- Aguja añadida aquí -->
        </div>
        <p>¡Haz clic para girar la ruleta!</p>
    {/if}

    {#if selectedTheme && !currentQuestion}
        <p>El tema seleccionado es: <strong>{selectedTheme}</strong></p>
        <p>Esperando la pregunta...</p>
    {/if}

    {#if currentQuestion}
        <p>¡El tema seleccionado es: <strong>{selectedTheme}</strong>!</p>
        <div class="question-card">
            {#if currentQuestion.question_image}
                <img src={currentQuestion.question_image} alt="Pregunta" style="width: 100%; border-radius: 8px; margin-bottom: 10px;" />
            {/if}
            <p>{currentQuestion.question_text}</p>
            <button class="answer-button" data-answer="a" on:click={() => selectAnswer('a')}>{ currentQuestion.option_a}</button>
            <button class="answer-button" data-answer="b" on:click={() => selectAnswer('b')}>{currentQuestion.option_b}</button>
            <button class="answer-button" data-answer="c" on:click={() => selectAnswer('c')}>{currentQuestion.option_c}</button>
            <button class="answer-button" data-answer="d" on:click={() => selectAnswer('d')}>{currentQuestion.option_d}</button>
        </div>
    {/if}

    <div class="timer">Tiempo restante: {timeLeft} segundos</div> <!-- Temporizador añadido aquí -->
    <button class="restart-button" on:click={restartGame}>Reiniciar juego</button>
</div>