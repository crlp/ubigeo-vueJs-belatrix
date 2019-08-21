<template>
  <div>
    <div>
      <h3>Seleccione un archivo</h3>
      <input type="file" @change="loadTextFromFile" />
    </div>
    <div>
      <h2>------------------------</h2>
      <h2>Resultado</h2>
      <h2>------------------------</h2>
      <h3>Departamentos</h3>
      <table>
        <thead>
          <tr>
            <th>Código</th>
            <th>Nombre</th>
            <th>Codigo padre</th>
            <th>Descripcion padre</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in departamentos" :key="`departamento-${index}`">
            <td>{{item.codigo}}</td>
            <td>{{item.descripcion}}</td>
            <td>--</td>
            <td>--</td>
          </tr>
        </tbody>
      </table>
    </div>
    <div>
      <h3>Provincias</h3>
      <table>
        <thead>
          <tr>
            <th>Código</th>
            <th>Nombre</th>
            <th>Codigo padre</th>
            <th>Descripcion padre</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in provincias" :key="`provincia-${index}`">
            <td>{{item.codigo}}</td>
            <td>{{item.descripcion}}</td>
            <td>{{item.codigoDepartamento}}</td>
            <td>{{item.descripcionDepartamento}}</td>
          </tr>
        </tbody>
      </table>
    </div>
    <div>
      <h3>Distritos</h3>
      <table>
        <thead>
          <tr>
            <th>Código</th>
            <th>Nombre</th>
            <th>Codigo padre</th>
            <th>Descripcion padre</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in distritos" :key="`distrito-${index}`">
            <td>{{item.codigo}}</td>
            <td>{{item.descripcion}}</td>
            <td>{{item.codigoProvincia}}</td>
            <td>{{item.descripcionProvincia}}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  props: {},

  data() {
    return {
      types: { departamento: "dep", provincia: "pro", distrito: "dis" },
      departamentos: [],
      provincias: [],
      distritos: [],
      dataInitial: [],
      fileContent: String
    };
  },

  methods: {
    loadTextFromFile(ev) {
      const file = ev.target.files[0];
      const fileReader = new FileReader();
      const self = this;
      fileReader.onload = e => {
        var contentF = e.target.result;
        self.fileContent = contentF;
        var lines = self.fileContent.split("\n");
        lines.forEach(element => {
          let arrayContent = element.split("/");
          let arrayContentClear = [];
          arrayContent.forEach(element => {
            if (element.trim() != "") {
              arrayContentClear.push(element);
            }
          });
          self.dataInitial.push(arrayContentClear);
        });
        this.llenarData();
      };
      fileReader.readAsText(file);
    },

    setDataArray(value, type) {
      if (type && value) {
        let arrHijo = [];
        let arrPadre = [];
        let arrNivel0 = [];
        let objItem = {};

        if (type === this.types.departamento) {
          arrHijo = this.detectFirstSpace(value[0].trim());
          objItem = { codigo: arrHijo[0], descripcion: arrHijo[1] };
          if (!this.existInArray(this.departamentos, objItem.codigo)) {
            this.departamentos.push(objItem);
          }
        } else if (type === this.types.provincia) {
          arrHijo = this.detectFirstSpace(value[1].trim());
          arrPadre = this.detectFirstSpace(value[0].trim());
          objItem = {
            codigo: arrHijo[0],
            descripcion: arrHijo[1],
            codigoDepartamento: arrPadre[0],
            descripcionDepartamento: arrPadre[1]
          };
          if (!this.existInArray(this.provincias, objItem.codigo)) {
            this.provincias.push(objItem);
          }
        } else if (type === this.types.distrito) {
          arrHijo = this.detectFirstSpace(value[2].trim());
          arrNivel0 = this.detectFirstSpace(value[1].trim());
          arrPadre = this.detectFirstSpace(value[0].trim());
          objItem = {
            codigo: arrHijo[0],
            descripcion: arrHijo[1],
            codigoDepartamento: arrPadre[0],
            descripcionDepartamento: arrPadre[1],
            codigoProvincia: arrNivel0[0],
            descripcionProvincia: arrNivel0[1]
          };
          if (!this.existInArray(this.distritos, objItem.codigo)) {
            this.distritos.push(objItem);
          }
        }
      }
    },
    llenarData() {
      let indicador = 0;
      this.dataInitial.forEach(item => {
        this.setDataArray(item, this.types.departamento);
        indicador = item.length;
        switch (indicador) {
          case 2:
            this.setDataArray(item, this.types.provincia);
            break;
          case 3:
            this.setDataArray(item, this.types.distrito);
            break;
          default:
            break;
        }
      });
    },

    existInArray(array, codDepartamento) {
      let c = 0;
      array.forEach(item => {
        if (item.codigo === codDepartamento) {
          c++;
        }
      });
      return c > 0;
    },

    detectFirstSpace(text) {
      let arrayClean = [];
      for (let i = -1; (i = text.indexOf(" ", i + 1)) != -1; i++) {
        arrayClean.push(text.substring(0, i));
        arrayClean.push(text.substring(i + 1, text.length));
      }
      return arrayClean;
    }
  }
};
</script>
<style>

td, th {
  border: 1px solid #ddd;
  padding: 8px;
}

tr:nth-child(even){background-color: #f2f2f2;}

tr:hover {background-color: #ddd;}

th {
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: left;
  background-color: #e1eae2;
}

body{
  padding: 16px;
}
</style>
