<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Para Ana ❤️</title>
    <style>
        :root {
            --fondo: #fcf8f8;
            --rosa-mate: #d6a6a6;
            --texto: #5a4a4a;
            --borde: #e8d5d5;
        }
        body { margin: 0; background: var(--fondo); font-family: 'Georgia', serif; overflow: hidden; touch-action: none; user-select: none; color: var(--texto); }
        canvas { position: fixed; inset: 0; z-index: 1; pointer-events: none; }
        
        /* Contenedores */
        .capa { position: fixed; inset: 0; display: flex; flex-direction: column; justify-content: center; align-items: center; z-index: 10; text-align: center; padding: 20px; }
        .oculto { display: none !important; }

        /* Botones */
        .btn {
            background: white; border: 1px solid var(--rosa-mate); color: var(--rosa-mate);
            padding: 12px 25px; border-radius: 4px; cursor: pointer; font-size: 1rem;
            pointer-events: all; transition: 0.3s; letter-spacing: 2px;
        }
        .btn:hover { background: var(--rosa-mate); color: white; }

        /* Poemas */
        .poema { position: relative; z-index: 5; pointer-events: none; max-width: 500px; }
        .verso { font-size: 1.1rem; opacity: 0; transition: 1.5s; margin: 12px 0; font-style: italic; line-height: 1.6; }
        .visible { opacity: 1; transform: translateY(-5px); }

        /* Juego */
        .flor { position: absolute; font-size: 50px; cursor: pointer; z-index: 20; pointer-events: all; filter: sepia(0.2); }
        #guia { position: fixed; top: 20px; font-size: 0.7rem; letter-spacing: 3px; opacity: 0.6; z-index: 5; }
    </style>
</head>
<body>

    <canvas id="canvas"></canvas>
    <div id="guia" class="oculto">RECOLECTA LA HISTORIA</div>

    <!-- PANTALLA 1 -->
    <div id="escena1" class="capa">
        <h1 style="font-weight: normal; letter-spacing: 10px; margin-bottom: 5px;">ANA</h1>
        <p style="font-style: italic; opacity: 0.5; margin-bottom: 30px;">Un año floreciendo juntos...</p>
        <button class="btn" onclick="iniciarNivel(1)">ENTRAR</button>
    </div>

    <!-- CONTENEDOR POEMAS -->
    <div class="capa" id="contenedor-poemas" style="pointer-events: none;">
        <div id="bloque1" class="poema oculto">
            <div class="verso v1">Parece que fue ayer cuando el tiempo se detuvo...</div>
            <div class="verso v1">y en la fragilidad de una <b>margarita</b> te encontré.</div>
            <div class="verso v1">Han pasado los meses como un suspiro mudo,</div>
            <div class="verso v1">y en cada invierno, en tu luz me refugié.</div>
            <div class="verso v1">Eres el <b>girasol</b> que en mis sombras se mantuvo,</div>
            <div class="verso v1">buscando siempre el sol que yo mismo olvidé.</div>
        </div>
        <div id="bloque2" class="poema oculto">
            <div class="verso v2">Pero el amor madura y se vuelve fuego ardiente,</div>
            <div class="verso v2">como una <b>amapola</b> que estalla en el trigal.</div>
            <div class="verso v2">Te amo de una forma profunda y valiente,</div>
            <div class="verso v2">un sentimiento eterno, puro y visceral.</div>
            <div class="verso v2">Eres mi <b>rosa</b> roja, mi norte y mi presente,</div>
            <div class="verso v2">el sueño hecho carne, mi paz espiritual.</div>
        </div>
    </div>

    <!-- BOTÓN INTERMEDIO -->
    <div id="capa-intermedia" class="capa oculto">
        <button class="btn" onclick="iniciarNivel(2)">CONTINUAR</button>
    </div>

    <!-- FINAL -->
    <div id="escena-final" class="capa oculto">
        <h2 style="font-weight: normal; letter-spacing: 3px;">Feliz Aniversario, Ana</h2>
        <p style="opacity: 0.7;">Por muchos años más de colores a tu lado.</p>
    </div>

    <script>
        const canvas = document.getElementById('canvas');
        const ctx = canvas.getContext('2d');
        let particulas = [];
        let cuenta = 0;
        let fase = 0;

        function resize() {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
        }
        window.addEventListener('resize', resize);
        resize();

        class Particula {
            constructor(x, y, char, color, firework = false) {
                this.x = x; this.y = y;
                this.char = char;
                this.vida = 1;
                this.size = firework ? Math.random() * 30 + 20 : Math.random() * 15 + 10;
                this.vX = (Math.random() - 0.5) * (firework ? 12 : 4);
                this.vY = (Math.random() - 0.5) * (firework ? 12 : 4);
                this.vY += firework ? -2 : 0;
            }
            update() {
                this.x += this.vX; this.y += this.vY;
                this.vida -= 0.01;
                if(this.vida < 0) this.vida = 0;
            }
            draw() {
                ctx.globalAlpha = this.vida;
                ctx.font = `${this.size}px serif`;
                ctx.fillText(this.char, this.x, this.y);
            }
        }

        function loop() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            particulas = particulas.filter(p => p.vida > 0);
            particulas.forEach(p => { p.update(); p.draw(); });
            requestAnimationFrame(loop);
        }
        loop();

        function iniciarNivel(n) {
            fase = n;
            document.getElementById('escena1').classList.add('oculto');
            document.getElementById('capa-intermedia').classList.add('oculto');
            document.getElementById('guia').classList.remove('oculto');
            document.getElementById('bloque' + n).classList.remove('oculto');
            spawnFlor();
        }

        function spawnFlor() {
            const limite = fase === 1 ? 6 : 12;
            if (cuenta >= limite) {
                if (fase === 1) document.getElementById('capa-intermedia').classList.remove('oculto');
                else lanzarFuegos();
                return;
            }

            const f = document.createElement('div');
            f.className = 'flor';
            const tipos = fase === 1 ? ['🌼', '🌻'] : ['🌺', '🌹'];
            f.innerText = tipos[Math.floor(Math.random() * tipos.length)];
            f.style.left = (Math.random() * 70 + 15) + 'vw';
            f.style.top = (Math.random() * 60 + 20) + 'vh';
            document.body.appendChild(f);

            f.onclick = (e) => {
                for(let i=0; i<10; i++) particulas.push(new Particula(e.clientX, e.clientY, f.innerText));
                const versos = document.querySelectorAll('.v' + fase);
                const index = fase === 1 ? cuenta : cuenta - 6;
                if(versos[index]) versos[index].classList.add('visible');
                cuenta++;
                f.remove();
                setTimeout(spawnFlor, 400);
            };
        }

        function lanzarFuegos() {
            document.getElementById('guia').classList.add('oculto');
            document.getElementById('escena-final').classList.remove('oculto');
            document.getElementById('contenedor-poemas').style.opacity = '0.4';
            
            setInterval(() => {
                const x = Math.random() * canvas.width;
                const y = Math.random() * canvas.height;
                const flores = ['🌸', '🌹', '🌻', '🌼', '❤️'];
                const f = flores[Math.floor(Math.random() * flores.length)];
                for(let i=0; i<20; i++) particulas.push(new Particula(x, y, f, null, true));
            }, 600);
        }

        // Rastro sutil al mover
        window.addEventListener('pointermove', (e) => {
            if(Math.random() > 0.9) particulas.push(new Particula(e.clientX, e.clientY, '✨'));
        });
    </script>
</body>
</html>

