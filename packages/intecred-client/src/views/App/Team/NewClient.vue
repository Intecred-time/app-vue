<script>
import axios from 'axios';
import { mapState, mapGetters, mapMutations } from 'vuex';
// import api from '../../../services/api';
import Label from '../../../components/Label.vue';
import IntModal from '../../../components/Modal.vue';
import IconPlus from '../../../assets/svgs/icon-plus.svg';
import IntInput from '../../../components/Input.vue';
import IntUpload from '../../../components/Upload.vue';
// import Upload from '../../../components/Upload.vue';
import IconCheck from '../../../assets/svgs/icon-checked.svg';
import IconArrow from '../../../assets/svgs/icon-arrow-left.svg';
import IconDelete from '../../../assets/svgs/icon-delete.svg';
import IconDanger from '../../../assets/svgs/icon-draft-reject.svg';
// import store from '../../../store';

export default {
  components: {
    Label,
    IntModal,
    IntInput,
    IconPlus,
    IntUpload,
    IconCheck,
    IconArrow,
    IconDelete,
    IconDanger,
    // Upload,
  },

  data: () => ({
    step: 0,
    form: {

      tipocliente: 'PF',
      rg: '',
      numcar: '',
      nome: '',
      razaosocial: '',
      nomefantasia: '',
      numinscricaoprodrural: '',
      cnpj: '',
      uf: '',
      cep: '',
      cidade: '',
      numero: '',
      rua: '',
      regional: '',
      complemento: '',
      bairro: '',
      cpf: '',
      estadocivil: '',
      conjuge: {
        rg: '',
        cpf: '',
        nome: '',
        email: '',
      },
      contato: {
        emailendereco: [
          {
            emailendereco: '',
          },
        ],
        numero: [
          {
            numero: '',
          },
        ],
        observacao: '',
      },
      representante: {
        rg: '',
        cpf: '',
        nome: '',
        estadocivil: '',
        uf: '',
        cep: '',
        cidade: '',
        endereco: '',
        regional: '',
        bairro: '',
        profissao: '',
        nacionalidade: '',
      },
      documentospessoais: [],
      comprovantesendereco: [],
      atasassembleias: [],
      contratos: [],
    },
    feedback: false,
    alertClientData: false,
    alertClientPJData: false,
  }),

  computed: {
    steps_pj() {
      return [
        {
          path: 'identificacao',
          label: 'Identificação',
        },
        {
          path: 'endereco',
          label: 'Endereço',
        },
        {
          path: 'contatos',
          label: 'Contatos',
        },
        {
          path: 'documentos',
          label: 'Documentos pessoais',
        },
        {
          path: 'representantes',
          label: 'Representante',
        },
      ];
    },

    steps_pf() {
      return [
        {
          path: 'identificacao',
          label: 'Identificação',
        },
        {
          path: 'endereco',
          label: 'Endereço',
        },
        {
          path: 'contatos',
          label: 'Contatos',
        },
        {
          path: 'documentos',
          label: 'Documentos pessoais',
        },
      ];
    },

    estadocivil() {
      return [
        {
          value: 'Solteiro',
          label: 'Solteiro',
        },
        {
          value: 'Casado',
          label: 'Casado',
        },
        {
          value: 'Divorciado',
          label: 'Divorciado',
        },
        {
          value: 'Convivente',
          label: 'Convivente',
        },
        {
          value: 'Viuvo',
          label: 'Viúvo',
        },
        {
          value: 'Outro',
          label: 'Outro',
        },
      ];
    },

    ...mapState({
      // empresaIdUsuario: 'empresaIdUsuario',
    }),
    ...mapGetters({
      empresaIdUsuario: 'empresaIdUsuario',
    }),

  },

  methods: {
    ...mapMutations('UPDATE_FILES'),

    load_cep() {
      // const url_cep;
      this.form.cep = this.form.cep.trim().replace(/[^0-9]/g, '');
      // this.cep = 'https://viacep.com.br/ws/' + this.cep + '/json';
      // clear all headers axios to viacep
      // axios.defaults.headers.common = null;
      axios.get(`https://cors-anywhere.herokuapp.com/https://viacep.com.br/ws/${this.form.cep}/json`).then((response) => {
        this.form.rua = response.data.logradouro;
        this.form.bairro = response.data.bairro;
        this.form.uf = response.data.uf;
        this.form.cidade = response.data.localidade;
      }).catch((error) => {
        console.log(error.statusText);
      });
    },

    hasPendingClientInput() {
      let hasPendency = false;
      if ((this.form.nome === '' || this.form.nome === null)
      || (this.form.cpf === '' || this.form.cpf === null)) {
        hasPendency = true;
      }
      return hasPendency;
    },

    hasPendingClientPJInput() {
      let hasPendencyPJ = false;
      if ((this.form.razaosocial === '' || this.form.razaosocial === null)
      || (this.form.cnpj === '' || this.form.cnpj === null)) {
        hasPendencyPJ = true;
      }
      return hasPendencyPJ;
    },

    closeAlertModal() {
      this.alertClientData = false;
      this.alertClientPJData = false;
    },

    load_cep_rep() {
      // const url_cep;
      this.form.representante.cep = this.form.representante.cep.trim().replace(/[^0-9]/g, '');
      // this.cep = 'https://viacep.com.br/ws/' + this.cep + '/json';
      // clear all headers axios to viacep
      // axios.defaults.headers.common = null;
      axios.get(`https://cors-anywhere.herokuapp.com/https://viacep.com.br/ws/${this.form.representante.cep}/json`).then((response) => {
        this.form.representante.endereco = response.data.logradouro;
        this.form.representante.bairro = response.data.bairro;
        this.form.representante.uf = response.data.uf;
        this.form.representante.cidade = response.data.localidade;
      }).catch((error) => {
        console.log(error.statusText);
      });
    },
    back() {
      this.$router.go(-1);
    },

    clear() {
      /**
       * @todo clear data
       */
      this.feedback = false;
    },

    async submit() {
      // let i;
      // const zeroFill = (n) => (`0${n}`).slice(-2);
      // const now = new Date();
      // const {
      //   tipocliente,
      //   conjuge,
      //   contato,
      //   representante,
      // } = this.form;

      // const user = tipocliente === 'CPF' ? conjuge : representante;
      const { form } = this;
      // console.log(form.contato.emailendereco);

      // this.$store.dispatch('submitCliente', form);
      console.log('usuarioId >> ', this.empresaIdUsuario);
      const auth = localStorage.getItem('auth_token');
      const token = localStorage.getItem('empresa_Id').toString();
      console.log('auth >> ', auth);
      console.log('token >> ', token);
      console.log('TipoCliente >>', this.form.tipocliente);
      if (this.form.tipocliente === 'PF') {
        if (this.hasPendingClientInput()) {
          this.alertClientData = true;
        } else {
          await axios.post(`${this.$url}/cliente`, form,
            {
              headers:
              {
                Authorization: `Bearer ${auth}`,
                empresaId: token,
              },
            })
            .then(async ({ data }) => {
              this.feedback = true;

              // const form1 = new FormData();
              // for (let i = 0; i < form.documentospessoais.length; i += 1) {
              //   form1.append(`file[${i}]`, form.documentospessoais[i].raw);
              // }

              // const formData = new FormData();
              const formData = new FormData();
              // const formData2 = new FormData();

              form.documentospessoais.forEach((file) => {
                formData.append('documentospessoais', file.file.raw);
              });
              form.comprovantesendereco.forEach((file) => {
                formData.append('comprovantesendereco', file.file.raw);
              });
              console.log('FormData 0102 ->', formData);
              // form.comprovantesresidencias.forEach((file) => {
              //   const json = JSON.stringify(file);
              //   const blob = new Blob([json], {
              //     type: 'application/json',
              //   });
              //   formData.append('comprovantesendereco', blob, file.name);
              // });

              if (form.documentospessoais.length > 0
              || form.comprovantesendereco.length > 0) {
                await axios.post(`${this.$url}/cliente/upload?clienteId=${data.toString()}`, formData,
                  {
                    headers: {
                      Authorization: `Bearer ${localStorage.getItem('auth_token')}`,
                      'Content-Type': 'multipart/form-data',
                    },
                  });
                console.log('deu certo, gravou imagem');
              }
            })
            .catch(() => {
              this.errors.submit = 'Desculpe, verifique os dados e tente novamente.';
            });
        }
      } else if (this.hasPendingClientPJInput()) {
        this.alertClientPJData = true;
      } else {
        console.log('PJ passa aqui');
        await axios.post(`${this.$url}/cliente`, form,
          {
            headers:
            {
              Authorization: `Bearer ${auth}`,
              empresaId: token,
            },
          })
          .then(async ({ data }) => {
            this.feedback = true;

            // const form1 = new FormData();
            // for (let i = 0; i < form.documentospessoais.length; i += 1) {
            //   form1.append(`file[${i}]`, form.documentospessoais[i].raw);
            // }

            // const formData = new FormData();

            // form.contratos.forEach((file) => {
            //   const json = JSON.stringify(file);
            //   const blob = new Blob([json], {
            //     type: 'application/json',
            //   });
            //   formData.append('contratos', blob, file.name);
            // });

            // form.atasassembleias.forEach((file) => {
            //   const json = JSON.stringify(file);
            //   const blob = new Blob([json], {
            //     type: 'application/json',
            //   });
            //   formData.append('atasassembleias', blob, file.name);
            // });

            const formData = new FormData();
            // const formData2 = new FormData();

            form.contratos.forEach((file) => {
              formData.append('contratos', file.file.raw);
            });
            form.atasassembleias.forEach((file) => {
              formData.append('atasassembleias', file.file.raw);
            });

            if (form.contratos.length > 0 || form.atasassembleias.length > 0) {
              await axios.post(`${this.$url}/cliente/upload?clienteId=${data.toString()}`, formData,
                {
                  headers: {
                    Authorization: `Bearer ${localStorage.getItem('auth_token')}`,
                    'Content-Type': 'multipart/form-data',
                  },
                });
              console.log('deu certo, gravou imagem');
            }
          })
          .catch(() => {
            this.errors.submit = 'Desculpe, verifique os dados e tente novamente.';
          });
      }
    },

    addValue(type) {
      if (type === 'numero') this.form.contato.numero.push({ numero: '' });

      if (type === 'emailendereco') this.form.contato.emailendereco.push({ emailendereco: '' });
    },

    deleteValue(type, index) {
      if (type === 'numero') this.form.contato.numero.splice(index, 1);

      if (type === 'emailendereco') this.form.contato.emailendereco.splice(index, 1);
    },
  },
};
</script>

<template>
  <div class="create-user">
    <div class="create-user__head">
      <RouterLink
        to="/clientes"
        class="p1"
      >
        <IconArrow />Clientes
      </RouterLink>

      <h2>Novo cliente</h2>
    </div>

    <div
      class="create-user__body"
      id="identificacao"
    >
      <ul class="create-user__steps"
      v-if="form.tipocliente === 'PF'"
      >
        <RouterLink
          class="create-user__steps-item p1"
          v-text="item.label"
          v-for="item in steps_pf"
          :key="item.path"
          :to="`#${item.path}`"
        />
      </ul>
       <ul class="create-user__steps"
      v-if="form.tipocliente === 'PJ'"
      >
        <RouterLink
          class="create-user__steps-item p1"
          v-text="item.label"
          v-for="item in steps_pj"
          :key="item.path"
          :to="`#${item.path}`"
        />
      </ul>
      <form
        class="create-user__form"
        @submit.prevent="submit"
        enctype="multipart/form-data"
      >

        <Label label="Identificação do Cliente" />

        <!-- Tipo do cliente -->
        <div class="create-user__form-type">
          <p class="p2">Tipo do cliente:</p>

          <el-radio-group v-model="form.tipocliente">
            <el-radio label="PF">Pessoa Física</el-radio>
            <el-radio label="PJ">Pessoa Jurídica</el-radio>
          </el-radio-group>
        </div>

        <!-- Informações básicas do cliente - CPF -->
        <div
          v-if="form.tipocliente === 'PF'"
          class="create-user__form-group"
        >
          <!-- Nome -->
          <div class="create-user__form-item">
            <int-input
              label="Nome do cliente"
              v-model="form.nome"
              placeholder="Nome completo"
              maxlength="100"
            />
          </div>

          <!-- CPF e RG -->
          <div class="create-user__form-item is-multi">
            <int-input
              label="CPF"
              v-model="form.cpf"
              placeholder="000.000.000-00"
              v-mask="'###.###.###-##'"
            />

            <int-input
              label="RG"
              v-model="form.rg"
              placeholder="000000000"
            />
          </div>

          <!-- Número de inscrição -->
          <div class="create-user__form-item">
            <int-input
              label="Número de inscrição de Produtor Rural"
              v-model="form.numinscricaoprodrural"
              placeholder="Número de inscrição de Produtor Rural"
              maxlength="30"
            />
          </div>

          <!-- Número do CAR -->
          <div class="create-user__form-item">
            <int-input
              label="Número do CAR (Cadastro Ambiental Rural)"
              v-model="form.numcar"
              placeholder="CAR (Cadastro Ambiental Rural)"
              maxlength="30"
            />
          </div>

          <!-- Estado civil -->
          <div class="create-user__form-item">
            <int-input
              label="Estado civil"
              v-model="form.estadocivil"
              placeholder="Selecione o estado civil"
              isSelect
            >

              <el-option
                v-for="item in estadocivil"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </int-input>
          </div>

          <!-- CPF e RG do cônjuge -->
          <div
            class="create-user__form-item is-multi"
            v-if="form.estadocivil === 'Casado'"
          >
            <int-input
              label="CPF do cônjuge"
              v-model="form.conjuge.cpf"
              placeholder="000.000.000-00"
              v-mask="'###.###.###-##'"
            />

            <int-input
              label="RG do cônjuge"
              v-model="form.conjuge.rg"
              placeholder="00.000.000-0"
              v-mask="'##.###.###-#'"
            />
          </div>

          <!-- Nome do cônjuge -->
          <div
            class="create-user__form-item"
            v-if="form.estadocivil === 'Casado'"
          >
            <int-input
              label="Nome do cônjuge"
              v-model="form.conjuge.nome"
              placeholder="Nome completo do cônjuge"
              maxlength="100"
            />
          </div>

          <!-- Email do cônjuge -->
          <div
            class="create-user__form-item"
            v-if="form.estadocivil === 'Casado'"
          >
            <int-input
              label="Email do cônjuge"
              v-model="form.conjuge.email"
              placeholder="Email do cônjuge"
              maxlength="100"
            />
          </div>

        </div>

        <!-- Informações básicas do cliente - CNPJ -->
        <div
          v-if="form.tipocliente === 'PJ'"
          class="create-user__form-group"
        >
          <!-- Razão social -->
          <div class="create-user__form-item">
            <int-input
              label="Razão social"
              v-model="form.razaosocial"
              placeholder="Razão social"
              maxlength="100"
            />
          </div>

          <!-- Nome fantasia e CNPJ -->
          <div class="create-user__form-item is-multi">
            <int-input
              label="Nome fantasia"
              v-model="form.nomefantasia"
              placeholder="Nome fantasia"
              maxlength="100"
            />

            <int-input
              label="CNPJ"
              v-model="form.cnpj"
              placeholder="00.000.000/0000-00"
              v-mask="'##.###.###/####-##'"
            />
          </div>
        </div>
        <!-- /CNPJ -->
        <div
          class="create-user__form-group"
          id="endereco"
        >
          <Label label="Endereço" />

          <!-- CEP -->
          <div class="create-user__form-item">
            <int-input
              label="CEP"
              v-model="form.cep"
              placeholder="00000-000"
              v-mask="'########'"
              v-on:input="load_cep"
            />
          </div>

          <!-- Endereço -->
          <div class="create-user__form-item">
            <int-input
              label="Endereço"
              v-model="form.rua"
              placeholder="Endereço completo do cliente"
              maxlength="100"
            />
          </div>

          <!-- Número e Complemento -->
          <div class="create-user__form-item is-multi">
            <int-input
              label="Número"
              v-model="form.numero"
              placeholder="Número"
              maxlength="5"
            />

            <int-input
              label="Complemento"
              v-model="form.complemento"
              placeholder="Complemento"
              maxlength="20"
            />
          </div>

          <!-- Bairro -->
          <div class="create-user__form-item">
            <int-input
              label="Bairro"
              v-model="form.bairro"
              placeholder="Bairro"
              maxlength="50"
            />
          </div>

          <!-- Cidade -->
          <div class="create-user__form-item">
            <int-input
              label="Cidade"
              v-model="form.cidade"
              placeholder="Cidade"
              maxlength="50"
            />
          </div>

          <!-- UF -->
          <div class="create-user__form-item">
            <int-input
              label="UF"
              v-model="form.uf"
              placeholder="UF"
              maxlength="2"
            />
          </div>

          <!-- Regional -->
          <div class="create-user__form-item">
            <int-input
              label="Regional"
              v-model="form.regional"
              placeholder="Digite a Regional do cliente"
              maxlength="30"
            />
          </div>
        </div>

        <!-- Informações do Representante - CNPJ -->
        <div
          v-if="form.tipocliente === 'PJ'"
          id="representantes"
          class="create-user__form-group"
        >
          <Label label="Representante de pessoa jurídica" />
          <!-- Nome do Representante -->
          <div class="create-user__form-item">
            <int-input
              label="Nome do Representante"
              v-model="form.representante.nome"
              placeholder="Nome completo"
              maxlength="100"
            />
          </div>

          <!-- CPF e RG -->
          <div class="create-user__form-item is-multi">
            <int-input
              label="CPF"
              v-model="form.representante.cpf"
              placeholder="000.000.000-00"
              v-mask="'###.###.###-##'"
            />

            <int-input
              label="RG"
              v-model="form.representante.rg"
              placeholder="000000000"
            />
          </div>
          <!-- Profissão -->
          <div class="create-user__form-item">
            <int-input
              label="Profissão"
              v-model="form.representante.profissao"
              placeholder="Profissão"
              maxlength="50"
            />
          </div>

          <!-- Nacionalidade e Estado civil -->
          <div class="create-user__form-item is-multi">
            <!-- Nacionalidade -->
            <int-input
              label="Nacionalidade"
              v-model="form.representante.nacionalidade"
              placeholder="Nacionalidade"
              maxlength="25"
            />

            <!-- Estado civil -->
            <int-input
              label="Estado civil"
              v-model="form.representante.estadocivil"
              placeholder="Selecione o estado civil"
              isSelect
            >
              <el-option
                v-for="item in estadocivil"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </int-input>
          </div>
          <!-- CEP -->
          <div class="create-user__form-item">
            <int-input
              label="CEP"
              v-model="form.representante.cep"
              placeholder="CEP da residencia"
              v-mask="'########'"
              v-on:input="load_cep_rep"
            />
          </div>

          <!-- Endereço -->
          <div class="create-user__form-item">
            <int-input
              label="Endereço"
              v-model="form.representante.endereco"
              placeholder="Endereço"
              maxlength="100"
            />
          </div>

          <!-- Bairro -->
          <div class="create-user__form-item">
            <int-input
              label="Bairro"
              v-model="form.representante.bairro"
              placeholder="Bairro da residência"
              maxlength="40"
            />
          </div>

          <!--UF, Municipio, Regional - Representante -->
          <!-- Municipio -->
          <div class="create-user__form-item">
            <int-input
              label="Município"
              v-model="form.representante.cidade"
              placeholder="Municipio da residência"
              maxlength="50"
            />
          </div>

          <!-- UF -->
          <div class="create-user__form-item">
            <int-input
              label="UF"
              v-model="form.representante.uf"
              placeholder="UF da residência"
              maxlength="2"
            />
          </div>

          <!-- Regional -->
          <div class="create-user__form-item">
            <int-input
              label="Regional"
              v-model="form.representante.regional"
              placeholder="Regional do Representante"
              maxlength="30"
            />
          </div>
          <!--Representante acaba aqui -->
        </div>

        <!-- Informações de contato do cliente -->
        <div
          class="create-user__form-group"
          id="contatos"
        >
          <Label label="Contatos" />

          <!-- Telefones -->
          <div class="create-user__form-item is-multi">
            <div class="group-input">
              <int-input
                v-for="(item, index) in form.contato.numero"
                :key="index"
                :label="`Telefone ${index > 0 ? index + 1 : ''}`"
                v-model="item.numero"
                placeholder="(xx) xxxx-xxxx"
                v-mask="['(##) ####-####', '(##) #####-####']"
              >
                <template #action>
                  <button
                    v-if="form.contato.numero.length > 1"
                    @click.prevent="deleteValue('numero', index)"
                    class="input__icon icon-button"
                  >
                    <IconDelete />
                  </button>
                </template>
              </int-input>
            </div>

            <button
              class="input__icon icon-button"
              @click.prevent="addValue('numero')"
            >
              <IconPlus />
            </button>
          </div>

          <!-- Emails -->
          <div class="create-user__form-item is-multi">
            <div class="group-input">
              <int-input
                v-for="(item, index) in form.contato.emailendereco"
                :key="index"
                :label="`Email ${index > 0 ? index + 1 : ''}`"
                v-model="item.emailendereco"
                placeholder="Digite o email"
                maxlength="60"
              >
                <template #action>
                  <button
                    v-if="form.contato.emailendereco.length > 1"
                    @click.prevent="deleteValue('emailendereco', index)"
                    class="input__icon icon-button"
                  >
                    <IconDelete />
                  </button>
                </template>
              </int-input>
            </div>

            <button
              class="input__icon icon-button"
              @click.prevent="addValue('emailendereco')"
            >
              <IconPlus />
            </button>
          </div>

          <!-- Observações -->
          <div class="create-user__form-item">
            <int-input
              label="Observações"
              v-model="form.contato.observacao"
              placeholder="Observações do contato"
              maxlength="100"
            />
          </div>
        </div>

        <!-- Informações de documento do cliente - CPF -->
        <div
          id="documentos"
          v-if="form.tipocliente === 'PF'"
          class="create-user__form-group"
        >
          <Label label="Documentos pessoais" />

          <int-upload
            title="Documentos pessoais"
            description="Como RG, CPF e CNH."
            v-model="form.documentospessoais"
          />

          <int-upload
            title="Comprovante de Endereço"
            description="Para atestar o endereço cadastrado."
            v-model="form.comprovantesendereco"
          />

          <!-- <Upload
            :update-before="true"
            :file-list="form.documentospessoais"
            @input="UPDATE_FILES"
            :is-multiple="true"
            v-model="form.documentospessoais"
            title="Documentos Pessoais"
            description="Documentos Pessoais.">
          </Upload> -->

        </div>

        <!-- Informações de documento do cliente - CNPJ -->
        <div
          id="documentos"
          v-if="form.tipocliente === 'PJ'"
          class="create-user__form-group"
        >
          <Label label="Documentos pessoais" />

          <int-upload
            title="Contrato ou Estatuto Social"
            description="Anexar última versão do contrato ou estatuto social da empresa."
            v-model="form.contratos"
          />

          <int-upload
            title="Última ata de assembléia"
            description="Para cooperativas."
            v-model="form.atasassembleias"
          />
        </div>

        <div class="create-user__form-actions">
          <el-button
            type="terciary"
            @click.prevent="back"
          >
            Cancelar
          </el-button>

          <el-button
            type="primary"
            native-type="submit"
          >
            Concluir
          </el-button>
        </div>
      </form>
    </div>

   <IntModal
      :show="alertClientData"
      :close-function="closeAlertModal"
      :draft-analyse="true">
      <template v-slot:head>
        <div class="modal__custom-head">
          <IconDanger/>
          <h2 class="modal__custom-head__danger">Atenção!</h2>
        </div>
      </template>
      <template v-slot:body>
        <div class="modal__custom-body">
          <p class="modal__custom-body__p2">Favor preencher Nome e CPF do cliente</p>
        </div>
      </template>
      <template v-slot:footer>
        <div class="modal__custom-footer">
          <el-button
            @click="closeAlertModal"
            type="danger"
          >Retornar
          </el-button>
        </div>
      </template>
    </IntModal>

   <IntModal
      :show="alertClientPJData"
      :close-function="closeAlertModal"
      :draft-analyse="true">
      <template v-slot:head>
        <div class="modal__custom-head">
          <IconDanger/>
          <h2 class="modal__custom-head__danger">Atenção!</h2>
        </div>
      </template>
      <template v-slot:body>
        <div class="modal__custom-body">
          <p class="modal__custom-body__p2">Favor preencher Razão Social e CNPJ do cliente</p>
        </div>
      </template>
      <template v-slot:footer>
        <div class="modal__custom-footer">
          <el-button
            @click="closeAlertModal"
            type="danger"
          >Retornar
          </el-button>
        </div>
      </template>
    </IntModal>

    <int-modal :show="feedback">
      <div class="modal-feedback">
        <div class="modal-feedback__message">
          <span>
            <IconCheck />
          </span>
          <h2>Cliente cadastrado com sucesso!</h2>
        </div>

        <div class="modal-feedback__actions">
          <el-button
            type="terciary"
            @click.prevent="back"
          >
            Visualizar clientes
          </el-button>

          <el-button
            type="primary"
            @click.prevent="clear"
          >
            Cadastrar novo cliente
          </el-button>
        </div>
      </div>
    </int-modal>
  </div>
</template>

<style lang="scss">
.create-user {
  &__head {
    top: 112px;
    display: flex;
    position: fixed;
    flex-direction: column;

    a {
      color: $--color-gray-6;
      display: flex;
      align-items: center;

      svg {
        margin-right: 10px;
      }
    }

    h2 {
      color: $--color-gray-7;
      margin-top: 4px;
    }
  }

  &__body {
    display: flex;
    margin-top: 36px;
  }

  &__steps {
    top: 212px;
    width: fit-content;
    padding: 32px;
    position: fixed;
    box-shadow: $--box-shadow-base;
    border-radius: 4px;
    background-color: $--color-gray-1;

    &-item {
      color: $--color-gray-6;
      cursor: pointer;
      display: flex;
      align-items: center;
      transition: all 0.4s ease-in-out;

      &:hover {
        color: $--color-gray-7;
      }

      &:not(:first-child) {
        margin-top: 12px;
      }

      &::before {
        width: 4px;
        height: 4px;
        content: '';
        display: flex;
        margin-right: 8px;
        border-radius: 4px;
        background-color: transparent;
      }

      &.router-link-active {
        color: $--color-primary;

        &::before {
          background-color: $--color-primary;
        }
      }
    }
  }

  &__form {
    width: 100%;
    margin: 100px 0 0 280px;

    &-type {
      display: flex;
      margin-top: 40px;
      align-items: center;

      p {
        color: $--color-gray-6;
      }

      .el-radio-group {
        margin-left: 30px;
      }
    }

    &-group {
      margin-top: 48px;

      .upload {
        margin-top: 28px;
      }
    }

    &-item {
      &.is-multi {
        display: flex;
        align-items: flex-end;

        .group-input {
          width: 100%;

          & > .input:not(:first-child) {
            margin-top: 24px;
          }
        }

        & > .input:not(:first-child) {
          margin-left: 16px;
        }
      }

      &:not(:first-child) {
        margin-top: 28px;
      }
    }

    &-actions {
      display: flex;
      border-top: 1px solid $--color-gray-5;
      margin-top: 32px;
      padding-top: 32px;
      align-items: center;
      justify-content: flex-end;

      .el-button {
        margin-left: 24px !important;
      }
    }
  }

  .modal-feedback {
    display: flex;
    flex-direction: column;

    &__message {
      display: flex;
      align-items: center;

      span {
        height: 64px;
        display: flex;
        min-width: 64px;
        align-items: center;
        border-radius: 50%;
        justify-content: center;
        background-color: $--color-success-light-2;
      }

      h2 {
        color: $--color-success;
        margin-left: 16px;
      }
    }

    &__actions {
      display: flex;
      margin-top: 36px;
      align-items: center;
    }
  }
}
</style>
