<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="10" md="12">
      <v-card>
        <v-card-title class="headline"> Integración </v-card-title>
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

        <div v-show="show_s13">
          <v-form ref="forms13">
            <v-row justify="space-between">
              <v-col cols="12" sm="5">
                <v-text-field
                  v-model="data_s13.f"
                  label="Ingrese la funcion"
                  type="text"
                  required
                ></v-text-field>

                <v-text-field
                  v-model="data_s13.n"
                  label="Ingrese el valor de n"
                  type="number"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12" sm="5">
                <v-text-field
                  v-model="data_s13.a"
                  label="Ingrese el valor de a"
                  type="number"
                  required
                ></v-text-field>

                <v-text-field
                  v-model="data_s13.b"
                  label="Ingrese el valor de b"
                  type="number"
                  required
                ></v-text-field>
              </v-col>
            </v-row>

            <v-btn @click="submitS13">Aceptar</v-btn>
          </v-form>
        </div>

        <div v-show="show_s38">
          <v-form ref="forms38">
            <v-row justify="space-between">
              <v-col cols="12" sm="5">
                <v-text-field
                  v-model="data_s38.f"
                  label="Ingrese la funcion"
                  type="text"
                  required
                ></v-text-field>

                <v-text-field
                  v-model="data_s38.xn"
                  label="Ingrese el valor de xn"
                  type="number"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12" sm="5">
                <v-text-field
                  v-model="data_s38.x0"
                  label="Ingrese el valor de x0"
                  type="number"
                  required
                ></v-text-field>
              </v-col>
            </v-row>

            <v-btn @click="submitS38">Aceptar</v-btn>
          </v-form>
        </div>

        <div v-show="show_s13l">
          <v-form ref="forms13l">
            <v-row justify="space-between">
              <v-col cols="12" sm="5">
                <v-text-field
                  v-model="data_s13l.x_list"
                  label="Ingrese los valores de x separados por comas"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12" sm="5">
                <v-text-field
                  v-model="data_s13l.y_list"
                  label="Ingrese los valores de y separados por comas"
                  type="text"
                  required
                ></v-text-field>
              </v-col>
            </v-row>

            <v-btn @click="submitS13l">Aceptar</v-btn>
          </v-form>
        </div>

        <div v-show="show_s38l">
          <v-form ref="forms13">
            <v-row justify="space-between">
              <v-col cols="12" sm="5">
                <v-text-field
                  v-model="data_s38l.x_list"
                  label="Ingrese los valores de x separados por comas"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12" sm="5">
                <v-text-field
                  v-model="data_s38l.y_list"
                  label="Ingrese los valores de y separados por comas"
                  type="text"
                  required
                ></v-text-field>
              </v-col>
            </v-row>

            <v-btn @click="submitS38l">Aceptar</v-btn>
          </v-form>
        </div>

        <div v-show="show_result">
          <v-text-field
            v-model="result"
            label="Resultado"
            type="text"
            required
            disabled
          ></v-text-field>
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
      items: [
        {
          name: "Simpson 1/3",
          value: "simpson13",
        },
        {
          name: "Simpson 3/8",
          value: "simpson38",
        },

        {
          name: "Simpson 1/3 con listas",
          value: "simpson13_list",
        },
        {
          name: "Simpson 3/8 con listas",
          value: "simpson38_list",
        },
      ],

      data_s13: {
        f: "",
        a: null,
        b: null,
        n: null,
      },

      data_s38: {
        f: "",
        x0: null,
        xn: null,
      },

      data_s13l: {
        x_list: "",
        y_list: "",
      },

      data_s38l: {
        x_list: "",
        y_list: "",
      },

      show_s13: true,
      show_s38: false,
      show_s13l: false,
      show_s38l: false,
      show_result: false,
    };
  },

  methods: {
    onChange(item) {
      switch (item.value) {
        case "simpson13":
          this.show_s13 = true;
          this.show_s38 = false;
          this.show_s13l = false;
          this.show_s38l = false;
          break;

        case "simpson38":
          this.show_s13 = false;
          this.show_s38 = true;
          this.show_s13l = false;
          this.show_s38l = false;
          break;

        case "simpson13_list":
          this.show_s13 = false;
          this.show_s38 = false;
          this.show_s13l = true;
          this.show_s38l = false;
          break;
        case "simpson38_list":
          this.show_s13 = false;
          this.show_s38 = false;
          this.show_s13l = false;
          this.show_s38l = true;
          break;
      }
    },

    async submitS13() {
      const URL = `${config.api}` + "/simpson_13";
      console.log(this.data_s13);
      var s13 = {
        a: parseFloat(this.data_s13.a),
        b: parseFloat(this.data_s13.b),
        n: parseFloat(this.data_s13.n),
        funcion: this.data_s13.f,
      };
      let { data } = await axios.post(URL, s13);
      this.result = `El resultado es ${data.res}`;
      this.show_result = true;
    },

    async submitS38() {
      const URL = `${config.api}` + "/simpson_38";

      var s38 = {
        x0: parseFloat(this.data_s38.x0),
        xn: parseFloat(this.data_s38.xn),
        funcion: this.data_s38.f,
      };

      let { data } = await axios.post(URL, s38);
      this.result = `El resultado es ${data.res}`;
      this.show_result = true;
    },

    async submitS13l() {
      const URL = `${config.api}` + "/simpson_13_list";
      let x_list = this.data_s13l.x_list.split(",");
      let y_list = this.data_s13l.y_list.split(",");
      if (x_list.length != y_list.length) {
        alert("El tamaño de las listas deben ser iguales");
        return;
      } else if (x_list.length < 3 || y_list.length < 3) {
        alert("El tamaño de las listas debe contener al menos 3 valores");
        return;
      } else {
        try {
          let s13l = {
            x: x_list.map((x) => parseFloat(x)),
            fx: y_list.map((x) => parseFloat(x)),
          };
          let { data } = await axios.post(URL, s13l);
          this.result = `El resultado es ${data.res}`;
          this.show_result = true;
        } catch (error) {
          alert(
            "Hubo un problema, por favor escriba los datos correctamente (numeros separados por comas"
          );
          return;
        }
      }
    },

    async submitS38l() {
      const URL = `${config.api}` + "/simpson_38_list";
      let x_list = this.data_s38l.x_list.split(",");
      let y_list = this.data_s38l.y_list.split(",");
      if (x_list.length != y_list.length) {
        alert("El tamaño de las listas deben ser iguales");
        return;
      } else if (x_list.length < 3 || y_list.length < 3) {
        alert("El tamaño de las listas debe contener al menos 3 valores");
        return;
      } else {
        try {
          let s38l = {
            x: x_list.map((x) => parseFloat(x)),
            fx: y_list.map((x) => parseFloat(x)),
          };
          let { data } = await axios.post(URL, s38l);
          this.result = `El resultado es ${data.res}`;
          this.show_result = true;
        } catch (error) {
          alert(
            "Hubo un problema, por favor escriba los datos correctamente (numeros separados por comas"
          );
          return;
        }
      }
    },
  },
};
</script>
