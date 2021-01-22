<template>
  <div id="app">
    <div class="container-fluid">
      <div class="row">
        <div class="col-md-12">
          <br />
          <div class="card">
            <div class="card-body text-center">
              <b>Turno de farmacias</b> a la fecha {{ fechas.fecha_acual }}
            </div>
            <br />
          </div>
          <br />
          <div class="card-body text-center">Selecciona Región y Ciudad</div>
          <div class="row">
            <div class="col-md-6">
              <div class="form-group">
                <label for="exampleFormControlSelect1"><b>REGIÓN</b></label>
                <select  v-model="regionSelect" class="form-control" id="exampleFormControlSelect1">
                  <option>Selecciona</option>
                  <option v-for="region in regiones" v-bind:key="region.id">{{region.NombreRegion}}</option>
                </select>
              </div>
            </div>
            <div class="col-md-3">
              <div class="form-group">
                <label for="exampleFormControlSelect1"><b>COMUNA</b></label>
                <select  class="form-control" id="exampleFormControlSelect1">
                  <option>Selecciona</option>
                </select>
              </div>
            </div>
            <div class="col-md-3">
              <div class="form-group">
                <button type="button" class="btn btn-outline-success search">BUSCAR</button>
              </div>
            </div>
          </div>

          <!-- <el-select v-model="regionSelect" placeholder="Select">
                  <el-option
                    v-for="region in regiones"
                    :key="region.NombreRegion"
                    :label="region.NombreRegion"
                    :value="region.id"
                  >
                  </el-option>
                </el-select> -->

          <div class="row">
            <farmacia
              v-for="farmacia in listarUsuariosPaginated"
              v-bind:key="farmacia.local_id"
              v-bind:farmacia="farmacia"
            />
          </div>
          <br />
        </div>
        <div class="card-footer clearfix">
          <ul class="pagination pagination-sm m-0 float-right">
            <li class="page-item" v-if="pageNumber > 0">
              <a href="#" class="page-link" @click.prevent="prevPage"
                >Anterior</a
              >
            </li>
            <li
              class="page-item"
              v-for="(page, index) in pagesList"
              :key="index"
              :class="[page == pageNumber ? 'active' : '']"
            >
              <a href="#" class="page-link" @click.prevent="selectPage(page)">{{
                page + 1
              }}</a>
            </li>
            <li class="page-item" v-if="pageNumber < pageCount - 1">
              <a href="#" class="page-link" @click.prevent="nextPage"
                >Siguiente</a
              >
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import moment from "moment";
import farmacia from "./components/Farmacia";

export default {
  name: "App",
  components: {
    farmacia,
  },
  created() {
    this.fechaActual();
  },
  data: function () {
    return {
      farmacias: [],
      fechas: {
        fecha_acual: "",
      },
      pageNumber: 0,
      perPage: 12,
      search: "",
      regionSelect: {},
      comunas: [],
      regiones: [
        {
          id: 1,
          NombreRegion: "Arica y Parinacota",
          comunas: ["Arica", "Camarones", "Putre", "General Lagos"],
        },
        {
          id: 2,
          NombreRegion: "Tarapacá",
          comunas: [
            "Iquique",
            "Alto Hospicio",
            "Pozo Almonte",
            "Camiña",
            "Colchane",
            "Huara",
            "Pica",
          ],
        },
        {
          id: 3,
          NombreRegion: "Antofagasta",
          comunas: [
            "Antofagasta",
            "Mejillones",
            "Sierra Gorda",
            "Taltal",
            "Calama",
            "Ollagüe",
            "San Pedro de Atacama",
            "Tocopilla",
            "María Elena",
          ],
        },
        {
          id: 4,
          NombreRegion: "Atacama",
          comunas: [
            "Copiapó",
            "Caldera",
            "Tierra Amarilla",
            "Chañaral",
            "Diego de Almagro",
            "Vallenar",
            "Alto del Carmen",
            "Freirina",
            "Huasco",
          ],
        },
        {
          id: 5,
          NombreRegion: "Coquimbo",
          comunas: [
            "La Serena",
            "Coquimbo",
            "Andacollo",
            "La Higuera",
            "Paiguano",
            "Vicuña",
            "Illapel",
            "Canela",
            "Los Vilos",
            "Salamanca",
            "Ovalle",
            "Combarbalá",
            "Monte Patria",
            "Punitaqui",
            "Río Hurtado",
          ],
        },
        {
          id: 6,
          NombreRegion: "Valparaíso",
          comunas: [
            "Valparaíso",
            "Casablanca",
            "Concón",
            "Juan Fernández",
            "Puchuncaví",
            "Quintero",
            "Viña del Mar",
            "Isla de Pascua",
            "Los Andes",
            "Calle Larga",
            "Rinconada",
            "San Esteban",
            "La Ligua",
            "Cabildo",
            "Papudo",
            "Petorca",
            "Zapallar",
            "Quillota",
            "Calera",
            "Hijuelas",
            "La Cruz",
            "Nogales",
            "San Antonio",
            "Algarrobo",
            "Cartagena",
            "El Quisco",
            "El Tabo",
            "Santo Domingo",
            "San Felipe",
            "Catemu",
            "Llaillay",
            "Panquehue",
            "Putaendo",
            "Santa María",
            "Quilpué",
            "Limache",
            "Olmué",
            "Villa Alemana",
          ],
        },
        {
          id: 7,
          NombreRegion: "Región Metropolitana de Santiago",
          comunas: [
            "Cerrillos",
            "Cerro Navia",
            "Conchalí",
            "El Bosque",
            "Estación Central",
            "Huechuraba",
            "Independencia",
            "La Cisterna",
            "La Florida",
            "La Granja",
            "La Pintana",
            "La Reina",
            "Las Condes",
            "Lo Barnechea",
            "Lo Espejo",
            "Lo Prado",
            "Macul",
            "Maipú",
            "Ñuñoa",
            "Pedro Aguirre Cerda",
            "Peñalolén",
            "Providencia",
            "Pudahuel",
            "Quilicura",
            "Quinta Normal",
            "Recoleta",
            "Renca",
            "San Joaquín",
            "San Miguel",
            "San Ramón",
            "Vitacura",
            "Puente Alto",
            "Pirque",
            "San José de Maipo",
            "Colina",
            "Lampa",
            "TilVl",
            "San Bernardo",
            "Buin",
            "Calera de Tango",
            "Paine",
            "Melipilla",
            "Alhué",
            "Curacaví",
            "María Pinto",
            "San Pedro",
            "Talagante",
            "El Monte",
            "Isla de Maipo",
            "Padre Hurtado",
            "Peñaflor",
          ],
        },
        {
          id: 8,
          NombreRegion: "Región del Libertador Gral. Bernardo O’Higgins",
          comunas: [
            "Rancagua",
            "Codegua",
            "Coinco",
            "Coltauco",
            "Doñihue",
            "Graneros",
            "Las Cabras",
            "Machalí",
            "Malloa",
            "Mostazal",
            "Olivar",
            "Peumo",
            "Pichidegua",
            "Quinta de Tilcoco",
            "Rengo",
            "Requínoa",
            "San Vicente",
            "Pichilemu",
            "La Estrella",
            "Litueche",
            "Marchihue",
            "Navidad",
            "Paredones",
            "San Fernando",
            "Chépica",
            "Chimbarongo",
            "Lolol",
            "Nancagua",
            "Palmilla",
            "Peralillo",
            "Placilla",
            "Pumanque",
            "Santa Cruz",
          ],
        },
        {
          id: 9,
          NombreRegion: "Región del Maule",
          comunas: [
            "Talca",
            "ConsVtución",
            "Curepto",
            "Empedrado",
            "Maule",
            "Pelarco",
            "Pencahue",
            "Río Claro",
            "San Clemente",
            "San Rafael",
            "Cauquenes",
            "Chanco",
            "Pelluhue",
            "Curicó",
            "Hualañé",
            "Licantén",
            "Molina",
            "Rauco",
            "Romeral",
            "Sagrada Familia",
            "Teno",
            "Vichuquén",
            "Linares",
            "Colbún",
            "Longaví",
            "Parral",
            "ReVro",
            "San Javier",
            "Villa Alegre",
            "Yerbas Buenas",
          ],
        },
        {
          id: 10,
          NombreRegion: "Región del Biobío",
          comunas: [
            "Concepción",
            "Coronel",
            "Chiguayante",
            "Florida",
            "Hualqui",
            "Lota",
            "Penco",
            "San Pedro de la Paz",
            "Santa Juana",
            "Talcahuano",
            "Tomé",
            "Hualpén",
            "Lebu",
            "Arauco",
            "Cañete",
            "Contulmo",
            "Curanilahue",
            "Los Álamos",
            "Tirúa",
            "Los Ángeles",
            "Antuco",
            "Cabrero",
            "Laja",
            "Mulchén",
            "Nacimiento",
            "Negrete",
            "Quilaco",
            "Quilleco",
            "San Rosendo",
            "Santa Bárbara",
            "Tucapel",
            "Yumbel",
            "Alto Biobío",
            "Chillán",
            "Bulnes",
            "Cobquecura",
            "Coelemu",
            "Coihueco",
            "Chillán Viejo",
            "El Carmen",
            "Ninhue",
            "Ñiquén",
            "Pemuco",
            "Pinto",
            "Portezuelo",
            "Quillón",
            "Quirihue",
            "Ránquil",
            "San Carlos",
            "San Fabián",
            "San Ignacio",
            "San Nicolás",
            "Treguaco",
            "Yungay",
          ],
        },
        {
          id: 11,
          NombreRegion: "Región de la Araucanía",
          comunas: [
            "Temuco",
            "Carahue",
            "Cunco",
            "Curarrehue",
            "Freire",
            "Galvarino",
            "Gorbea",
            "Lautaro",
            "Loncoche",
            "Melipeuco",
            "Nueva Imperial",
            "Padre las Casas",
            "Perquenco",
            "Pitrufquén",
            "Pucón",
            "Saavedra",
            "Teodoro Schmidt",
            "Toltén",
            "Vilcún",
            "Villarrica",
            "Cholchol",
            "Angol",
            "Collipulli",
            "Curacautín",
            "Ercilla",
            "Lonquimay",
            "Los Sauces",
            "Lumaco",
            "Purén",
            "Renaico",
            "Traiguén",
            "Victoria",
          ],
        },
        {
          id: 12,
          NombreRegion: "Región de Los Ríos",
          comunas: [
            "Valdivia",
            "Corral",
            "Lanco",
            "Los Lagos",
            "Máfil",
            "Mariquina",
            "Paillaco",
            "Panguipulli",
            "La Unión",
            "Futrono",
            "Lago Ranco",
            "Río Bueno",
          ],
        },
        {
          id: 13,
          NombreRegion: "Región de Los Lagos",
          comunas: [
            "Puerto Montt",
            "Calbuco",
            "Cochamó",
            "Fresia",
            "FruVllar",
            "Los Muermos",
            "Llanquihue",
            "Maullín",
            "Puerto Varas",
            "Castro",
            "Ancud",
            "Chonchi",
            "Curaco de Vélez",
            "Dalcahue",
            "Puqueldón",
            "Queilén",
            "Quellón",
            "Quemchi",
            "Quinchao",
            "Osorno",
            "Puerto Octay",
            "Purranque",
            "Puyehue",
            "Río Negro",
            "San Juan de la Costa",
            "San Pablo",
            "Chaitén",
            "Futaleufú",
            "Hualaihué",
            "Palena",
          ],
        },
        {
          id: 14,
          NombreRegion: "Región Aisén del Gral. Carlos Ibáñez del Campo",
          comunas: [
            "Coihaique",
            "Lago Verde",
            "Aisén",
            "Cisnes",
            "Guaitecas",
            "Cochrane",
            "O’Higgins",
            "Tortel",
            "Chile Chico",
            "Río Ibáñez",
          ],
        },
        {
          id: 15,
          NombreRegion: "Región de Magallanes y de la AntárVca Chilena",
          comunas: [
            "Punta Arenas",
            "Laguna Blanca",
            "Río Verde",
            "San Gregorio",
            "Cabo de Hornos (Ex Navarino)",
            "AntárVca",
            "Porvenir",
            "Primavera",
            "Timaukel",
            "Natales",
            "Torres del Paine",
          ],
        },
      ],
    };
  },
  mounted() {
    this.fetch();
  },
  computed: {
    pageCount() {
      //metodo para obtener el número de paginas
      let a = this.farmacias.length, //variable a almacena la cantidad de datos que hay en el array listUsuarios
        b = this.perPage; //variable almacena la cantidad de datos por pagina
      return Math.ceil(a / b); //funcion math.ceil devuelve el entero mayor de un número dado
    },
    listarUsuariosPaginated() {
      //obtener datos paginados
      let inicio = this.pageNumber * this.perPage,
        fin = inicio + this.perPage;

      return this.farmacias.slice(inicio, fin);
    },
    pagesList() {
      let a = this.farmacias.length,
        b = this.perPage;
      let pageCount = Math.ceil(a / b);
      let count = 0,
        pagesArray = [];

      while (count < pageCount) {
        pagesArray.push(count);
        count++;
      }
      return pagesArray;
    },
  },
  methods: {
    cargarComunas() {
      this.comunas = this.regionSelect;
      console.log(this.comunas);
    },
    nextPage() {
      this.pageNumber++;
    },
    prevPage() {
      this.pageNumber--;
    },
    selectPage(page) {
      this.pageNumber = page;
    },
    inicializarPagination() {
      this.pageNumber = 0;
    },
    fechaActual() {
      this.fechas.fecha_acual = moment().format("DD-MM-YYYY");
    },
    fetch() {
      var url = "http://localhost:8080/index.php/ws/getLocalesTurnos/"
      axios
        .get(url)
        .then((response) => {
          this.inicializarPagination();
          this.farmacias = response.data;
        })
        .catch((error) => {
          console.log(error);
        });
    },
    changePage(page) {
      this.page = page <= 0 || page > this.pages ? this.page : page;
      this.fetch();
    },
    searchData() {
      this.page = 1;
      this.fetch();
    },
  },
};
</script>

<style>
.h1 {
  text-align: center;
}
.search{
  margin-top: 30px;
}
</style>
