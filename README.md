<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>矩阵行简化阶梯型演示</title>
    <style>
        body {
            font-family: Arial, sans-serif;
        }
        .container {
            display: flex;
            flex-direction: row;
            justify-content: space-between;
            padding: 20px;
        }
        .matrix-input {
            margin-right: 20px;
        }
        table {
            border-collapse: collapse;
        }
        th, td {
            border: 1px solid black;
            padding: 5px;
            text-align: center;
        }
        .steps {
            max-width: 400px;
            height: auto;
            overflow-y: auto;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="matrix-input">
            <h2>输入矩阵 (n 阶)</h2>
            <textarea id="matrix" rows="10" cols="30" placeholder="例如：&#10;1, 2, 3 &#10;4, 5, 6 &#10;7, 8, 9"></textarea><br><br>
            <button onclick="transformMatrix()">下一步</button>
        </div>
        <div class="steps">
            <h2>转化步骤</h2>
            <div id="transformationSteps"></div>
        </div>
    </div>
    <script>
        function parseMatrix(input) {
            const rows = input.trim().split('\n').map(row => row.split(',').map(Number));
            return rows;
        }

        function matrixToHTML(matrix) {
            const table = document.createElement('table');
            matrix.forEach(row => {
                const tr = document.createElement('tr');
                row.forEach(cell => {
                    const td = document.createElement('td');
                    td.textContent = cell.toFixed(2);
                    tr.appendChild(td);
                });
                table.appendChild(tr);
            });
            return table;
        }

        function transformMatrix() {
            const matrixInput = document.getElementById('matrix').value;
            let matrix = parseMatrix(matrixInput);
            const stepsContainer = document.getElementById('transformationSteps');
            stepsContainer.innerHTML = '';

            if (!matrix.length || !matrix[0].length) {
                alert('请输入有效的矩阵。');
                return;
            }

            let row = 0, col = 0;
            while (row < matrix.length && col < matrix[0].length) {
                // Find the pivot row
                let pivot = row;
                for (let i = row + 1; i < matrix.length; i++) {
                    if (Math.abs(matrix[i][col]) > Math.abs(matrix[pivot][col])) {
                        pivot = i;
                    }
                }

                // Swap rows
                [matrix[row], matrix[pivot]] = [matrix[pivot], matrix[row]];

                // Add current step
                const stepTitle = document.createElement('h4');
                stepTitle.textContent = `第${row + 1}步：交换第${row + 1}行和第${pivot + 1}行，使第${row + 1}行成为主行`;
                stepsContainer.appendChild(stepTitle);
                stepsContainer.appendChild(matrixToHTML(matrix));

                // Normalize pivot row
                const pivotValue = matrix[row][col];
                if (pivotValue !== 0) {
                    matrix[row] = matrix[row].map(val => val / pivotValue);

                    // Add normalization step
                    const normTitle = document.createElement('h4');
                    normTitle.textContent = `归一化第${row + 1}行的主元`;
                    stepsContainer.appendChild(normTitle);
                    stepsContainer.appendChild(matrixToHTML(matrix));
                }

                // Eliminate below
                for (let i = row + 1; i < matrix.length; i++) {
                    const factor = matrix[i][col];
                    matrix[i] = matrix[i].map((val, j) => val - factor * matrix[row][j]);
                }

                // Add elimination step
                const elimTitle = document.createElement('h4');
                elimTitle.textContent = `消去第${row + 1}行以下各行的第${col + 1}列`;
                stepsContainer.appendChild(elimTitle);
                stepsContainer.appendChild(matrixToHTML(matrix));

                row++;
                col++;
            }
        }
    </script>
</body>
</html>
