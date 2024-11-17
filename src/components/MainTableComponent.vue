<template>
  <q-table
    fullscreen
    flat
    bordered
    title="Casos Epidemiológicos - Santa Rosa de Cabal"
    :filter="filter"
    :loading="isLoading"
    :rows="
      rows?.filter(
        (anyData) => anyData.a_o_de_contagio == selectedYear || !selectedYear
      )
    "
    :columns="[
      {
        name: 'id',
        required: true,
        label: 'No.',
        align: 'left',
        field: (row) => row.id,
        format: (val) => `${val}`,
        sortable: true,
      },
      {
        name: 'dateOfInfection',
        align: 'center',
        label: 'Fecha de contagio',
        field: (row) => row.fecha_de_contagio,
        sortable: true,
      },
      {
        name: 'yearOfInfection',
        align: 'center',
        label: 'Año del contagio',
        field: (row) => row.a_o_de_contagio,
        sortable: true,
      },
      {
        name: 'monthOfInfection',
        align: 'center',
        label: 'Mes del contagio',
        field: (row) => row.mes_de_contagio,
        sortable: true,
      },

      {
        name: 'age',
        align: 'center',
        label: 'Edad',
        field: (row) => row.edad,
        sortable: true,
      },
      {
        name: 'gender',
        align: 'center',
        label: 'Sexo',
        field: (row) => row.sexo,
        sortable: true,
      },
      {
        name: 'stratum',
        align: 'center',
        label: 'Estrato',
        field: (row) => row.estrato,
        sortable: true,
      },

      {
        name: 'eventName',
        align: 'left',
        label: 'Nombre del evento',
        field: (row) => row.nombre_del_evento,
        sortable: true,
      },
    ]"
    :visible-columns="visibleColumns"
    row-key="id"
    :pagination="{
      sortBy: 'id',
      descending: false,
      page: 1,
      rowsPerPage: 30,
    }"
  >
    <template v-slot:top>
      <div class="col-xs-12 col-sm-6 q-table__title">
        Casos Epidemiológicos - Santa Rosa de Cabal
      </div>

      <q-space />

      <q-select
        v-model="visibleColumns"
        multiple
        outlined
        dense
        options-dense
        display-value="columnas visibles"
        emit-value
        map-options
        :options="columns"
        option-label="name"
        option-value="value"
        options-cover
        style="min-width: 150px"
      />
      <q-select
        outlined
        dense
        debounce="300"
        v-model="selectedYear"
        clearable
        :options="[2022, 2023, 2024]"
        label="Año"
        style="width: 150px"
      >
        <template v-slot:append>
          <q-icon name="event" />
        </template>
      </q-select>
      <q-input
        outlined
        dense
        debounce="300"
        v-model="filter"
        placeholder="Buscar ..."
        clearable
      >
        <template v-slot:append>
          <q-icon name="search" />
        </template>
      </q-input>
    </template>

    <template v-slot:body="props">
      <q-tr :props="props">
        <q-td key="id" :props="props">
          {{ props.row?.id + 1 }}
        </q-td>
        <q-td key="dateOfInfection" :props="props">
          {{ props.row.fecha_de_contagio.split('T')[0].replaceAll('-', '/') }}
        </q-td>
        <q-td key="yearOfInfection" :props="props">
          {{ props.row.a_o_de_contagio }}
        </q-td>
        <q-td key="monthOfInfection" :props="props">
          <div class="text-bold">
            {{ getMonthName(props.row.mes_de_contagio) }}
          </div>
        </q-td>
        <q-td key="age" :props="props">
          {{ props.row.edad }}
        </q-td>
        <q-td key="gender" :props="props">
          <div class="text-bold">
            <span v-if="props.row.sexo == 'M'" class="text-blue">M</span>
            <span v-else-if="props.row.sexo == 'F'" class="text-pink">F</span>
          </div>
        </q-td>
        <q-td key="stratum" :props="props">
          {{ props.row.estrato }}
        </q-td>

        <q-td key="eventName" :props="props">
          {{ props.row.nombre_del_evento }}
        </q-td>
      </q-tr>
    </template>
  </q-table>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import { api } from 'src/boot/axios';

interface IData {
  id?: string;
  fecha_de_notificaci_n: string;
  semana: string;
  a_o: string;
  edad: string;
  codigo_nacionalidad: string;
  nombre_nacionalidad: string;
  sexo: string;
  codigo_departamento: string;
  codigo_municipio: string;
  tipo_seguridad_social: string;
  estrato: string;
  discapacidad: string;
  desplazado: string;
  migrante: string;
  carcelario: string;
  gestante: string;
  indigena: string;
  icbf: string;
  desmovilizado: string;
  psiquiatrico: string;
  victima_de_violencia: string;
  otros_grupos: string;
  fecha_de_contagio: string;
  hospitalizacion: string;
  fecha_hospitalizacion: string;
  certificado_de_defunci_n: string;
  nombre_del_evento: string;
  unidad_primaria_generadora: string;
  mes_de_contagio: string;
  a_o_de_contagio: string;
}

export default defineComponent({
  name: 'MainTableComponent',

  setup() {
    const rows = ref<IData[]>([]);

    const isLoading = ref(true);
    const filter = ref('');
    const selectedYear = ref('');
    const columns = [
      { name: 'Fecha de contagio', value: 'dateOfInfection' },
      { name: 'Año de contagio', value: 'yearOfInfection' },
      { name: 'Mes de contagio', value: 'monthOfInfection' },
      { name: 'Edad', value: 'age' },
      { name: 'Sexo', value: 'gender' },
      { name: 'Estráto', value: 'stratum' },
      { name: 'Nombre del evento', value: 'eventName' },
    ];
    const visibleColumns = ref([
      'dateOfInfection',
      'yearOfInfection',
      'monthOfInfection',
      'age',
      'gender',
      'stratum',
      'eventName',
    ]);

    const getData = async () => {
      try {
        isLoading.value = true;
        // Traer datos
        const res = await api.get(
          'https://www.datos.gov.co/resource/xc7d-5tmm.json'
        );
        if (res) {
          res.data.map((data: { id: string }, index: string) => {
            data.id = index;
            return data;
          });
          rows.value = res.data;
        }
      } catch (error) {
      } finally {
        isLoading.value = false;
      }
    };
    getData();

    const getMonthName = (monthNumber: number): string => {
      const months = [
        'Enero',
        'Febrero',
        'Marzo',
        'Abril',
        'Mayo',
        'Junio',
        'Julio',
        'Agosto',
        'Septiembre',
        'Octubre',
        'Noviembre',
        'Diciembre',
      ];
      if (monthNumber < 1 || monthNumber > 12) {
        throw new Error('El número debe estar entre 1 y 12');
      }
      return months[monthNumber - 1];
    };

    return {
      rows,
      isLoading,
      filter,
      selectedYear,
      columns,
      visibleColumns,
      getMonthName,
    };
  },
});
</script>
