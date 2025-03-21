<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Reserva de Asientos - Asamblea 20 de abril</title>
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js";
        import { getDatabase, ref, onValue, set } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-database.js";

        const firebaseConfig = {
            apiKey: "AIzaSyAKP4w62Q3lPQYr30zzGf4rs3iF83uJCuc",
            authDomain: "buses-32e31.firebaseapp.com",
            databaseURL: "https://buses-32e31-default-rtdb.firebaseio.com",
            projectId: "buses-32e31",
            storageBucket: "buses-32e31.firebasestorage.app",
            messagingSenderId: "914612323057",
            appId: "1:914612323057:web:8dde205f394adcb9845f50",
            measurementId: "G-GT9Z1BVNFQ"
        };

        const app = initializeApp(firebaseConfig);
        const db = getDatabase(app);
        const adminPassword = "33951"; // Cambia esto por tu clave secreta

        document.addEventListener("DOMContentLoaded", () => {
            const busesContainer = document.getElementById("buses");
            const busCount = 2;
            const seatsPerBus = 40;

            function createBus(busNumber) {
                let busDiv = document.createElement("div");
                busDiv.classList.add("bus");
                let title = document.createElement("h3");
                title.innerText = `Bus ${busNumber}`;
                busDiv.appendChild(title);
                
                for (let i = 1; i <= seatsPerBus; i++) {
                    let seat = document.createElement("div");
                    seat.classList.add("seat");
                    seat.innerText = i;
                    
                    const seatRef = ref(db, `bus${busNumber}/seat${i}`);
                    onValue(seatRef, snapshot => {
                        let data = snapshot.val();
                        if (data) {
                            seat.innerText = data.name;
                            seat.classList.add(data.paid ? "paid" : "reserved");
                        } else {
                            seat.innerText = i;
                            seat.classList.remove("reserved", "paid");
                        }
                    });
                    
                    seat.onclick = function () {
                        if (!seat.classList.contains("reserved")) {
                            let name = prompt("Ingresa tu nombre para reservar el asiento");
                            if (name) {
                                set(seatRef, { name, paid: false });
                            }
                        } else if (confirm("¿Quieres marcar este asiento como pagado?")) {
                            let password = prompt("Ingresa la clave de administrador");
                            if (password === adminPassword) {
                                set(seatRef, { name: seat.innerText, paid: !seat.classList.contains("paid") });
                            } else {
                                alert("Clave incorrecta. No tienes permiso para marcar como pagado.");
                            }
                        }
                    };
                    busDiv.appendChild(seat);
                }
                busesContainer.appendChild(busDiv);
            }
            
            for (let i = 1; i <= busCount; i++) {
                createBus(i);
            }
        });
    </script>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
        }
        .bus {
            display: inline-block;
            margin: 20px;
            padding: 10px;
            border: 2px solid black;
        }
        .seat {
            width: 80px;
            height: 40px;
            margin: 5px;
            display: inline-block;
            border: 1px solid gray;
            text-align: center;
            line-height: 40px;
            cursor: pointer;
            background-color: lightgray;
            font-size: 12px;
            overflow: hidden;
            white-space: nowrap;
        }
        .reserved {
            background-color: red;
            color: white;
        }
        .paid {
            background-color: green;
            color: white;
        }
    </style>
</head>
<body>
    <h1>Transporte asamblea del 20 de abril</h1>
    <p>Haz clic en un asiento para reservarlo. Cuando realices el pago del transporte ($12.000) se marcará con color verde, el plazo máximo para reservar y pagar el puesto es el 13 de abril.</p>
    <div id="buses"></div>
</body>
</html>
