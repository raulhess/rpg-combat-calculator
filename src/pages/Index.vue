<template>
  <q-page class="flex flex-center">
    <q-card class="q-ma-md">
      <q-toolbar class="bg-primary text-white">
        <q-toolbar-title>RPG Combat Calculator</q-toolbar-title>
      </q-toolbar>
      <q-card-section>
        <div class="text-subtitle2">Número de Faces do dado</div>
        <q-input v-model="diceFaces" outlined dense></q-input>
        <br />
        <div class="text-subtitle2">Criatura A</div>
        <div class="row q-col-gutter-x-sm">
          <div class="col">
            <q-input v-model="aAtk" label="Ataque" outlined dense></q-input>
          </div>
          <div class="col">
            <q-input v-model="aDef" label="Defesa" outlined dense></q-input>
          </div>
          <div class="col">
            <q-input v-model="aHitPoints" label="Vida" outlined dense></q-input>
          </div>
        </div>
        <div class="row q-col-gutter-x-sm q-mt-sm">
          <div class="col">
            <q-input v-model="aInit" label="Iniciativa" outlined dense></q-input>
          </div>
        </div>
        <br />
        <div class="text-subtitle2">Criatura B</div>
        <div class="row q-col-gutter-x-sm">
          <div class="col">
            <q-input v-model="bAtk" label="Ataque" outlined dense></q-input>
          </div>
          <div class="col">
            <q-input v-model="bDef" label="Defesa" outlined dense></q-input>
          </div>
          <div class="col">
            <q-input v-model="bHitPoints" label="Vida" outlined dense></q-input>
          </div>
        </div>
        <div class="row q-col-gutter-x-sm q-mt-sm">
          <div class="col">
            <q-input v-model="bInit" label="Iniciativa" outlined dense></q-input>
          </div>
        </div>
        <br />
        <div class="text-subtitle2">Quantas simulações?</div>
        <q-input v-model="simulationCount" outlined dense></q-input>
        <div class="q-pt-md text-right">
          <q-btn color="primary" label="Calcular" @click="simulate()"></q-btn>
        </div>
      </q-card-section>
    </q-card>
  </q-page>
</template>

<script>
export default {
  name: "PageIndex",
  data() {
    return {
      diceFaces: 6,
      aAtk: 0,
      aDef: 0,
      bAtk: 0,
      bDef: 0,
      aInit: 0,
      bInit: 0,
      aHitPoints: 3,
      bHitPoints: 3,
      simulationCount: 10,
    };
  },

  methods: {
    simulate() {
      const media = (arr) =>
        arr.reduce((sum, el) => sum + el.rounds, 0) / arr.length;
      const vitorias = (arr, winner) =>
        arr.reduce((sum, el) => sum + (el.winner === winner ? 1 : 0), 0);

      let simulacoes = [];
      for (let count = 0; count < this.simulationCount; count++) {
        let rounds = 0;
        let aInit = 0;
        let bInit = 0;
        do {
          aInit =
            Math.floor(Math.random() * this.diceFaces) +
            1 +
            parseInt(this.aInit);
          bInit =
            Math.floor(Math.random() * this.diceFaces) +
            1 +
            parseInt(this.bInit);
        } while (aInit === bInit);
        let aTurn = aInit > bInit;
        let internalAHitpoints = this.aHitPoints;
        let internalBHitpoints = this.bHitPoints;
        while (internalAHitpoints > 0 && internalBHitpoints > 0) {
          if (aTurn) {
            // A ataca
            let aAtk =
              Math.floor(Math.random() * this.diceFaces) +
              1 +
              parseInt(this.aAtk);
            let bDef =
              Math.floor(Math.random() * this.diceFaces) +
              1 +
              parseInt(this.bDef);
            // console.log(`A atk ${aAtk} vs. B def ${bDef}`);
            if (aAtk > bDef) {
              internalBHitpoints -= 1;
            }
            aTurn = false;
          } else {
            // B ataca
            let bAtk =
              Math.floor(Math.random() * this.diceFaces) +
              1 +
              parseInt(this.bAtk);
            let aDef =
              Math.floor(Math.random() * this.diceFaces) +
              1 +
              parseInt(this.aDef);
            // console.log(`B atk ${bAtk} vs. A def ${aDef}`);
            if (bAtk > aDef) {
              internalAHitpoints -= 1;
            }
            aTurn = true;
          }
          // console.log(
          //   `Turno ${rounds}: [A = ${internalAHitpoints}hp / A = ${internalBHitpoints}hp]`
          // );
          rounds += 1;
        }
        // console.log(
        //   `Simulação ${count + 1}: [${rounds - 1} turnos / ${
        //     internalAHitpoints > 0 ? "A ganhou" : "B ganhou"
        //   }]`
        // );
        simulacoes.push({
          rounds: rounds - 1,
          winner: internalAHitpoints > 0 ? "A" : "B",
        });
      }
      let mediaDeTurnos = media(simulacoes);
      let vitoriasA = vitorias(simulacoes, "A");
      let vitoriasB = vitorias(simulacoes, "B");
      console.log(
        `----------## Média de turnos: {${Math.round(
          mediaDeTurnos
        )}} ##----------`
      );
      console.log(
        `------## Porcentagem de vitórias A: {${Math.round(
          (vitoriasA * 100) / simulacoes.length
        )}%} ##------`
      );
      console.log(
        `------## Porcentagem de vitórias B: {${Math.round(
          (vitoriasB * 100) / simulacoes.length
        )}%} ##------`
      );
    },
  },
};
</script>
