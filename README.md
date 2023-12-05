# node.js

const readline = require('readline');
const fs = require('fs');
const path = require('path');
const { performance } = require('perf_hooks');

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
});

function readAndProcessFile(filePath) {
  const startTime = performance.now();

  let sumOfNumbers = 0;
  let textLinesCount = 0;

  const inputStream = fs.createReadStream(filePath);
  const rlFile = readline.createInterface({
    input: inputStream,
    crlfDelay: Infinity
  });

  rlFile.on('line', (line) => {
    if (/^\d+$/.test(line)) {
      // Line contains only numbers
      sumOfNumbers += parseInt(line, 10);
    } else {
      // Line contains text
      textLinesCount++;
    }
  });

  rlFile.on('close', () => {
    const endTime = performance.now();
    const executionTime = (endTime - startTime) / 1000; // Convert to seconds

    console.log('Resumo:');
    console.log(`- Soma dos números: ${sumOfNumbers}`);
    console.log(`- Linhas com texto: ${textLinesCount}`);
    console.log(`- Tempo de execução: ${executionTime.toFixed(2)} segundos`);

    rl.question('Deseja executar novamente? (yes/no): ', (answer) => {
      if (answer.toLowerCase() === 'yes') {
        rl.question('Digite o caminho do arquivo: ', (newFilePath) => {
          readAndProcessFile(newFilePath);
        });
      } else {
        rl.close();
      }
    });
  });
}

rl.question('Digite o caminho do arquivo: ', (filePath) => {
  readAndProcessFile(filePath);
});
