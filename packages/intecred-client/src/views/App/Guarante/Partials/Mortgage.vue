<template>
  <div class="container__form-data">
    <div class="container__form-data__radio-group">
      <el-radio-group v-model="tipo">
        <el-radio label="Hipoteca">hipoteca</el-radio>
        <el-radio label="Alienacao">Alienação</el-radio>
        <el-radio label="HipotecaoAlienacao">hipoteca com alienação</el-radio>
      </el-radio-group>
    </div>
    <div class="container__form-data__size-top container__form-data__size-bottom">
      <IntInput
        v-model="tipo_hipoteca"
        label="Tipo da hipoteca"
        placeholder="Defina o tipo da hipoteca"
        isSelect
      >
        <el-option
          v-for="(item, index) in mortgageType"
          :key="index"
          :label="item.label"
          :value="item.value"
        >
          <span class="el-select__option-child">{{ item.label }}</span>
          <span class="el-select__option-child">{{ item.label }}</span>
        </el-option>
      </IntInput>
    </div>
    <div class="container__form-data__size-bottom">
      <IntInput
        v-model="descricaoImovel"
        label="Descrição do imóvel"
        placeholder="Descreva o imóvel"
      />
    </div>
    <div class="container__form-data__size-bottom">
      <IntInput
        v-model="grauHipoteca"
        label="Grau da hipoteca"
        placeholder="Selecione o grau"
        isSelect
      >
        <el-option
          v-for="(item, index) in mortgageGrade"
          :key="index"
          :label="item.label"
          :value="item.value"
        >
          <span class="el-select__option-child">{{ item.label }}</span>
          <span class="el-select__option-child">{{ item.label }}</span>
        </el-option>
      </IntInput>
    </div>
    <div class="container__form-data__size-bottom">
      <IntInput
        v-model="numeroMatricula"
        label="Número da matrícula"
        placeholder="Número da matrícula "
      />
    </div>
    <div class="container__form-data__item--two-items container__form-data__size-bottom">
      <span class="container__form-data__item--two-items__item">
        <IntInput
          v-model="ufComarcaRegistro"
          label="UF da Comarca de registro"
          placeholder="Selecione o estado"
          isSelect
          @input="ObterMunicipiosComarca(ufComarcaRegistro)"
        >
          <el-option
            v-for="(item, index) in mortgageUf"
            :key="index"
            :label="item.uf"
            :value="item.estadoId"
          >
            <span class="el-select__option-child">{{ item.uf }}</span>
            <span class="el-select__option-child">{{ item.uf }}</span>
          </el-option>
        </IntInput>
      </span>
      <span class="container__form-data__item--two-items__item">
        <IntInput
          v-model="cidadeComarcaRegistro"
          label="Município da Comarca de registro"
          placeholder="Selecione a cidade"
          isSelect
        >
          <el-option
            v-for="(item, index) in mortgageCity"
            :key="index"
            :label="item.nome"
            :value="item.municipioId"
          >
            <span class="el-select__option-child">{{ item.nome }}</span>
            <span class="el-select__option-child">{{ item.nome }}</span>
          </el-option>
        </IntInput>
      </span>
    </div>
    <div class="container__form-data__size-bottom">
      <Upload
        @input="UPDATE_FILES_MORTGAGE"
        :is-multiple="true"
        v-model="file"
        title="Importar arquivos"
        description="Referentes à propriedade, como imagens ou arquivos de mapa em *kml.">
      </Upload>
    </div>
    <IntInput
      v-model="obs"
      class="container__form-data__item"
      label="Obervações"
      placeholder="Observações sobre a hipoteca"
    />
  </div>
</template>

<script>
import axios from 'axios';
import { mapState, mapMutations } from 'vuex';
import IntInput from '../../../../components/Input.vue';
import Upload from '../../../../components/Upload.vue';
// import api from '../../../../services/api';

export default {
  name: 'Mortgage',
  mounted() {
    this.getMortgageOptions();
  },
  data: () => ({
    // mortgageType: [],
    // mortgageGrade: [],
    mortgageUf: [],
    mortgageCity: [],
  }),
  methods: {
    ...mapMutations('guarantee', ['UPDATE_PARTIALS_GUARANTEE_MORTGAGE', 'UPDATE_FILES_MORTGAGE']),

    async getMortgageOptions() {
    //   await api.get('/mortgage_options')
    //     .then((res) => {
    //       const {
    //         mortgage_type: mortgageType,
    //         mortgage_grade: mortgageGrade,
    //         mortgage_uf: mortgageUf,
    //         mortgage_city: mortgageCity,
    //       } = res.data;
    //       this.mortgageType = mortgageType;
    //       this.mortgageGrade = mortgageGrade;
    //       this.mortgageUf = mortgageUf;
    //       this.mortgageCity = mortgageCity;
    //     });
      await axios.get(`${this.$url}/garantia/ObterEstados`,
        {
          headers:
          {
            Authorization: `Bearer ${localStorage.getItem('auth_token')}`,
            empresaId: localStorage.getItem('empresa_Id').toString(),
          },
        })
        .then((res) => {
          this.mortgageUf = res.data;
        });
    },
    async ObterMunicipiosComarca(ufEstadoComarcaId) {
      await axios.get(`${this.$url}/garantia/ObterMunicipiosPorUF?ufEstadoId=${ufEstadoComarcaId}`,
        {
          headers:
          {
            Authorization: `Bearer ${localStorage.getItem('auth_token')}`,
            empresaId: localStorage.getItem('empresa_Id').toString(),
          },
        })
        .then((res) => {
          this.mortgageCity = res.data;
        });
    },
  },
  components: {
    IntInput,
    Upload,
  },
  computed: {
    ...mapState('guarantee', ['guarantee']),
    file: {
      get() {
        return this.guarantee.hipoteca.file || [];
      },
      set(value) {
        this.UPDATE_PARTIALS_GUARANTEE_MORTGAGE({
          key: 'file',
          value,
        });
      },
    },
    obs: {
      get() {
        return this.guarantee.hipoteca.obs;
      },
      set(value) {
        this.UPDATE_PARTIALS_GUARANTEE_MORTGAGE({
          key: 'obs',
          value,
        });
      },
    },
    cidadeComarcaRegistro: {
      get() {
        return this.guarantee.hipoteca.cidadeComarcaRegistro;
      },
      set(value) {
        this.UPDATE_PARTIALS_GUARANTEE_MORTGAGE({
          key: 'cidadeComarcaRegistro',
          value,
        });
      },
    },
    ufComarcaRegistro: {
      get() {
        return this.guarantee.hipoteca.ufComarcaRegistro;
      },
      set(value) {
        this.UPDATE_PARTIALS_GUARANTEE_MORTGAGE({
          key: 'ufComarcaRegistro',
          value,
        });
      },
    },
    numeroMatricula: {
      get() {
        return this.guarantee.hipoteca.numeroMatricula;
      },
      set(value) {
        this.UPDATE_PARTIALS_GUARANTEE_MORTGAGE({
          key: 'numeroMatricula',
          value,
        });
      },
    },
    grauHipoteca: {
      get() {
        return this.guarantee.hipoteca.grauHipoteca;
      },
      set(value) {
        this.UPDATE_PARTIALS_GUARANTEE_MORTGAGE({
          key: 'grauHipoteca',
          value,
        });
      },
    },
    descricaoImovel: {
      get() {
        return this.guarantee.hipoteca.descricaoImovel;
      },
      set(value) {
        this.UPDATE_PARTIALS_GUARANTEE_MORTGAGE({
          key: 'descricaoImovel',
          value,
        });
      },
    },
    tipo: {
      get() {
        return this.guarantee.hipoteca.tipo || 'mortgage';
      },
      set(value) {
        this.UPDATE_PARTIALS_GUARANTEE_MORTGAGE({
          key: 'tipo',
          value,
        });
      },
    },
    tipo_hipoteca: {
      get() {
        return this.guarantee.hipoteca.tipo_hipoteca;
      },
      set(value) {
        this.UPDATE_PARTIALS_GUARANTEE_MORTGAGE({
          key: 'tipo_hipoteca',
          value,
        });
      },
    },
    mortgageType() {
      return [
        {
          value: 'ImovelRural',
          label: 'Imóvel Rural',
        },
        {
          value: 'ImovelUrbano',
          label: 'Imóvel Urbano',
        },
      ];
    },
    mortgageGrade() {
      return [
        {
          value: '1Grau',
          label: '1º Grau',
        },
        {
          value: '2Grau',
          label: '2º Grau',
        },
        {
          value: '3GRau',
          label: '3º Grau',
        },
        {
          value: '4GRau',
          label: '4º Grau',
        },
      ];
    },
  },
};
</script>
<style scoped lang="scss" src="../styles/style.scss"/>
