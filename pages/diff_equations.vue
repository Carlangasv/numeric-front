<template>
  <v-row justify="center" align="center">
    <script src="https://cdn.plot.ly/plotly-latest.min.js"></script>
    <v-col cols="12" sm="10" md="12">
      <v-card>
        <v-card-title class="headline"> Ecuaciones Diferenciales </v-card-title>
        <br />
        <v-card-text> Seleccione la funcion a usar </v-card-text>

        <v-combobox
          v-model="selected"
          :items="items"
          item-text="name"
          item-value="value"
          label="Seleccione"
          filled
          outlined
          :return-object="true"
          @change="onChange"
          >{{ items.name }}</v-combobox
        >

        <div v-show="show_rk">
          <v-form ref="form_rk_4">
            <v-row justify="space-between">
              <v-col cols="12" sm="5">
                <v-text-field
                  v-model="data_rk.f"
                  label="Ingrese la funcion"
                  type="text"
                  required
                ></v-text-field>

                <v-text-field
                  v-model="data_rk.xi"
                  label="Ingrese el valor de xi"
                  type="number"
                  required
                ></v-text-field>

                <v-text-field
                  v-model="data_rk.x"
                  label="Ingrese el valor de x"
                  type="number"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12" sm="5">
                <v-text-field
                  v-model="data_rk.h"
                  label="Ingrese el valor de h"
                  type="number"
                  required
                ></v-text-field>

                <v-text-field
                  v-model="data_rk.xf"
                  label="Ingrese el valor de xf"
                  type="number"
                  required
                ></v-text-field>

                <v-text-field
                  v-model="data_rk.y"
                  label="Ingrese el valor de y"
                  type="number"
                  required
                ></v-text-field>
              </v-col>
            </v-row>

            <v-btn @click="submitRK">Aceptar</v-btn>
          </v-form>
        </div>

        <div v-show="show_rk_edo">
          <v-form ref="form_rk_edo">
            <v-row justify="space-between">
              <v-col cols="12" sm="5">
                <v-text-field
                  v-model="data_rk_edo.f[0]"
                  label="Ingrese la funcion 1"
                  type="text"
                  required
                ></v-text-field>

                <v-text-field
                  v-model="data_rk_edo.xi"
                  label="Ingrese el valor de xi"
                  type="number"
                  required
                ></v-text-field>

                <v-text-field
                  v-model="data_rk_edo.h"
                  label="Ingrese el valor de h"
                  type="number"
                  required
                ></v-text-field>

                <v-text-field
                  v-model="data_rk_edo.ci.y1"
                  label="Ingrese el valor de y1"
                  type="number"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12" sm="5">
                <v-text-field
                  v-model="data_rk_edo.f[1]"
                  label="Ingrese la funcion 2"
                  type="text"
                  required
                ></v-text-field>

                <v-text-field
                  v-model="data_rk_edo.xf"
                  label="Ingrese el valor de xf"
                  type="number"
                  required
                ></v-text-field>

                <v-text-field
                  v-model="data_rk_edo.ci.x"
                  label="Ingrese el valor inicial  de x"
                  type="number"
                  required
                ></v-text-field>

                <v-text-field
                  v-model="data_rk_edo.ci.y2"
                  label="Ingrese el valor inicial  de y2"
                  type="number"
                  required
                ></v-text-field>
              </v-col>
            </v-row>

            <v-btn @click="submitRK4EDO">Aceptar</v-btn>
          </v-form>
        </div>
        <v-container v-show="graph">
        <div id="plt" style="width: 600px; height: 250px"></div>
         </v-container>
        <div>
          <v-simple-table dark v-show="tableEDO">
            <template v-slot:default>
              <thead>
                <tr>
                  <th class="text-left">X</th>
                  <th class="text-left">Y1</th>
                  <th class="text-left">Y2</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="item in itemsEDO" :key="item.x">
                  <td>{{ item.x }}</td>
                  <td>{{ item.y1 }}</td>
                  <td>{{ item.y2 }}</td>
                </tr>
              </tbody>
            </template>
          </v-simple-table>
        </div>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
import axios from "axios";
import config from "@/assets/config";

export default {
  data() {
    return {
      selected: null,
      headersEDO: ["x", "y1", "y2"],
      itemsEDO: [],
      tableEDO : false,
      graph : false,
      items: [
        {
          name: "Runge-Kutta 4to Orden",
          value: "cuarto_orden",
        },
        {
          name: "Runge-Kutta Orden Superior",
          value: "orden_superior",
        },

        {
          name: "Runge-Kutta 4to Orden ED Ordinaria",
          value: "cuarto_orden_edo",
        },
      ],

      data_rk: {
        f: "",
        h: null,
        xi: null,
        xf: null,
        xi: null,
        y: null,
      },

      data_rk_edo: {
        f: ["", ""],
        h: null,
        xi: null,
        xf: null,
        xi: null,
        ci: {
          x: null,
          y1: null,
          y2: null,
        },
      },

      show_rk: false,
      show_rk_edo: false,
    };
  },

  methods: {
    onChange(item) {
      this.tableEDO = false
      this.graph = false
      switch (item.value) {
        case "cuarto_orden":
          this.show_rk = true;
          this.show_rk_edo = false;
          break;

        case "orden_superior":
          this.show_rk = true;
          this.show_rk_edo = false;
          break;

        case "cuarto_orden_edo":
          this.show_rk = false;
          this.show_rk_edo = true;
          break;
      }
    },

    async submitRK() {
      const URL = `${config.api}/` + this.selected.value;
      var rk = {
        h: parseFloat(this.data_rk.h),
        xf: parseFloat(this.data_rk.xf),
        xi: parseFloat(this.data_rk.xi),
        x: parseFloat(this.data_rk.x),
        y: parseFloat(this.data_rk.y),
        funcion: this.data_rk.f,
      };
      let { data } = await axios.post(URL, rk);
      let plt = document.getElementById("plt");
      Plotly.newPlot(
        plt,
        [
          {
            x: data.x,
            y: data.y,
          },
        ],
        {
          margin: { t: 0 },
        }
      );
      this.tableEDO = false
      this.graph = true
    },

    async submitRK4EDO() {
      const URL = `${config.api}/` + this.selected.value;

      let ci = [
        [parseFloat(this.data_rk_edo.ci.x), parseFloat(this.data_rk_edo.ci.y1)],
        [parseFloat(this.data_rk_edo.ci.x), parseFloat(this.data_rk_edo.ci.y2)],
      ];

      var rk4EDO = {
        h: parseFloat(this.data_rk_edo.h),
        xf: parseFloat(this.data_rk_edo.xf),
        xi: parseFloat(this.data_rk_edo.xi),
        ci: ci,
        funciones: this.data_rk_edo.f,
      };
      let { data } = await axios.post(URL, rk4EDO);
      for (let i = 0; i < data.x.length; i++) {
        this.itemsEDO.push({ x: data.x[i], y1: data.y1[i], y2: data.y2[i] });
      }
      this.tableEDO = true
      this.graph = false
    },
  },
};
</script>
