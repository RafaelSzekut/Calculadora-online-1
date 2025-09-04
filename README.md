<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Calculadora Avançada + Minutos Automáticos</title>
<style>
    body {
        font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        background-color: #121212;
        color: #ffffff;
        margin: 0;
        padding: 20px;
    }
    h2 { text-align: center; margin-bottom: 20px; color: #00e0ff; }
    .inputs, .time-inputs { display: flex; flex-wrap: wrap; justify-content: center; gap: 15px; margin-bottom: 20px; }
    label { display: flex; flex-direction: column; font-size: 14px; margin-bottom: 5px; }
    input { padding: 8px; border-radius: 5px; border: none; width: 100px; background-color: #1e1e1e; color: #ffffff; text-align: center; }
    button { padding: 10px 20px; border-radius: 5px; border: none; background-color: #00e0ff; color: #121212; font-weight: bold; cursor: pointer; transition: background-color 0.3s; }
    button:hover { background-color: #00b8cc; }
    table { border-collapse: collapse; width: 100%; margin-top: 20px; }
    th, td { border: 1px solid #333; padding: 10px; text-align: center; }
    th { background-color: #1f1f1f; color: #00e0ff; }
    tbody tr:nth-child(even) { background-color: #1a1a1a; }
    tbody tr:hover { background-color: #222; }
</style>
</head>
<body>

<h2>Calculadora Avançada + Minutos Automáticos</h2>

<!-- Inputs de horário -->
<div class="time-inputs">
    <label>Entrada (hh:mm): <input type="text" id="horaEntrada" placeholder="0730" maxlength="5" oninput="formatarHora(this)"></label>
    <label>Saída (hh:mm): <input type="text" id="horaSaida" placeholder="1500" maxlength="5" oninput="formatarHora(this)"></label>
</div>

<!-- Inputs principais -->
<div class="inputs">
    <label>pc/h: <input type="text" id="pc_h"></label>
    <label>nC: <input type="text" id="nC"></label>
    <label>Lb: <input type="text" id="Lb"></label>
    <label>Pro: <input type="text" id="Pro"></label>
    <button onclick="calcular()">Calcular</button>
</div>

<!-- Tabela de resultados -->
<table id="resultadoTabela">
    <thead>
        <tr>
            <th>pc/h</th>
            <th>nC</th>
            <th>tt (minutos)</th>
            <th>Lb</th>
            <th>Req</th>
            <th>Pro</th>
            <th>Resultado Final (%)</th>
        </tr>
    </thead>
    <tbody></tbody>
</table>

<script>
// Formata automaticamente o input para hh:mm
function formatarHora(input) {
    let v = input.value.replace(/\D/g, '');
    if (v.length >= 3) {
        v = v.slice(0,2) + ':' + v.slice(2,4);
    }
    input.value = v;
}

// Converte vírgula para ponto
function parseVirgula(valor) {
    return parseFloat(valor.replace(',', '.'));
}

// Calcula minutos trabalhados com desconto específico
function calcularMinutos() {
    const entrada = document.getElementById('horaEntrada').value;
    const saida = document.getElementById('horaSaida').value;

    const [hEntrada, mEntrada] = entrada.split(':').map(Number);
    const [hSaida, mSaida] = saida.split(':').map(Number);

    if (isNaN(hEntrada) || isNaN(mEntrada) || isNaN(hSaida) || isNaN(mSaida)) {
        alert("Digite horários válidos no formato hh:mm");
        return null;
    }

    let minutos = (hSaida * 60 + mSaida) - (hEntrada * 60 + mEntrada);

    // Intervalo crítico 08:50 - 09:05
    const inicioCritico = 8 * 60 + 50;
    const fimCritico = 9 * 60 + 5;

    const inicioTrabalho = hEntrada * 60 + mEntrada;
    const fimTrabalho = hSaida * 60 + mSaida;

    // Desconto apenas se qualquer parte passar pelo intervalo crítico
    if (fimTrabalho > inicioCritico && inicioTrabalho < fimCritico) {
        minutos -= 15;
    }

    if (minutos < 0) minutos = 0;

    return minutos;
}

// Função principal
function calcular() {
    const pc_h = parseVirgula(document.getElementById('pc_h').value);
    const nC = parseVirgula(document.getElementById('nC').value);
    const Lb = parseVirgula(document.getElementById('Lb').value);
    const Pro = parseVirgula(document.getElementById('Pro').value);

    const tt = calcularMinutos();
    if (tt === null) return;

    if (isNaN(pc_h) || isNaN(nC) || isNaN(Lb) || isNaN(Pro) || Lb === 0) {
        alert("Por favor, insira todos os valores corretamente (Lb não pode ser zero).");
        return;
    }

    const Req = (pc_h * nC * tt) / (Lb * 60);
    const ResultadoFinal = (Pro * 100) / Req;

    const tbody = document.getElementById('resultadoTabela').querySelector('tbody');
    const row = tbody.insertRow();
    row.insertCell().textContent = pc_h.toString().replace('.', ',');
    row.insertCell().textContent = nC.toString().replace('.', ',');
    row.insertCell().textContent = tt.toString();
    row.insertCell().textContent = Lb.toString().replace('.', ',');
    row.insertCell().textContent = Req.toFixed(4).toString().replace('.', ',');
    row.insertCell().textContent = Pro.toString().replace('.', ',');
    row.insertCell().textContent = ResultadoFinal.toFixed(1).toString().replace('.', ',');

    // Limpa inputs
    document.getElementById('pc_h').value = '';
    document.getElementById('nC').value = '';
    document.getElementById('Lb').value = '';
    document.getElementById('Pro').value = '';
    document.getElementById('horaEntrada').value = '';
    document.getElementById('horaSaida').value = '';
}
</script>

</body>
</html>
