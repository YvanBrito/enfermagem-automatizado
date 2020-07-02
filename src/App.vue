<template>
  <div id="app">
    <b-form @submit="onSubmit" @reset="onReset">
      <b-form-group label="Medicamentos">
        <multiselect v-model="valueMed" :options="optionsMed" :multiple="true" group-values="libs" group-label="type" :group-select="true" placeholder="Type to search" track-by="name" label="name"><span slot="noResult">Oops! No elements found. Consider changing the search query.</span></multiselect>
      </b-form-group>

      <b-card
        title="CKD-EPI">
        <b-row>
          <b-col lg=3>
            <b-form-group id="input-idade" label="Faixa Etária:" label-for="input-idade">
              <b-form-input
                id="input-idade"
                v-model="form.idade.value"
                type="number"
                required
                placeholder="Idade"
              ></b-form-input>
            </b-form-group>
          </b-col>
          <b-col lg=3>
            <b-form-group id="input-creatinina" label="Creatinina:" label-for="input-creatinina">
              <b-form-input
                id="input-creatinina"
                v-model="form.creatinina.value"
                type="number"
                step="0.01"
                required
                placeholder="Creatinina"
              ></b-form-input>
            </b-form-group>
          </b-col>
          <b-col lg="3">
            <b-form-group label="Raça">
              <b-form-radio required v-model="form.raca.value" name="radios-raca" value="branco">Branco(a)</b-form-radio>
              <b-form-radio required v-model="form.raca.value" name="radios-raca" value="negro">Negro(a)</b-form-radio>
            </b-form-group>
          </b-col>

          <b-col lg="3">
            <b-form-group label="Sexo">
              <b-form-radio required v-model="form.sexo.value" name="radios-sexo" value="homem">Homem</b-form-radio>
              <b-form-radio required v-model="form.sexo.value" name="radios-sexo" value="mulher">Mulher</b-form-radio>
            </b-form-group>
          </b-col>
        </b-row>
        <b-row>
          <b-col>
            <div class="egfrresult">{{ (Math.round((form.egfr.value + Number.EPSILON) * 100) / 100) | formatNumber }} ml/min/1.73 m2</div>
          </b-col>
        </b-row>
      </b-card>

      <b-form-group id="input-ureia" label="Ureia:" label-for="input-ureia">
        <b-form-input
          id="input-ureia"
          v-model="form.ureia.value"
          type="number"
          required
          placeholder="Ureia"
        ></b-form-input>
      </b-form-group>

      <b-form-group label="Dieta">
        <multiselect v-model="valueDieta" tag-placeholder="Add this as new tag" placeholder="Search or add a tag" label="name" track-by="code" :options="optionsDieta" :multiple="true" :taggable="true"></multiselect>
      </b-form-group>

      <b-container>
        <b-row>
          <b-col lg="3">
            <b-form-group label="Diabetes">
              <b-form-radio required v-model="form.diabetes.value" name="radios-diabetes" value="sim">Sim</b-form-radio>
              <b-form-radio required v-model="form.diabetes.value" name="radios-diabetes" value="nao">Não</b-form-radio>
            </b-form-group>
          </b-col>

          <b-col lg="3">
            <b-form-group label="HAS">
              <b-form-radio required v-model="form.has.value" name="radios-has" value="sim">Sim</b-form-radio>
              <b-form-radio required v-model="form.has.value" name="radios-has" value="nao">Não</b-form-radio>
            </b-form-group>
          </b-col>

          
        </b-row>
      </b-container>

      <b-button type="submit" variant="primary">Resultado</b-button>
      <b-button type="reset" variant="danger">Resetar</b-button>
    </b-form>
    <div v-if="show">
      <b-table striped hover :items="tableItems"></b-table>
    </div>
  </div>
</template>

<script>
// https://vue-multiselect.js.org/
import Multiselect from 'vue-multiselect'

export default {
  components: { Multiselect },
  name: 'App',
  data() {
      return {
        optionsMed: [
          {
            type: 'Antibiótico',
            libs: [
              { name: 'Gentamicina' },
              { name: 'Estreptomicina' },
              { name: 'Amicacina' },
              { name: 'Tobramicina' },
              { name: 'Netilmicina' },
              { name: 'Neomicina' },
              { name: 'Espectiomicina' },
              { name: 'Anfotericina B' },
              { name: 'Vancomicina' },
              { name: 'Ciprofloxacina' },
              { name: 'Rifampicina' },
              { name: 'Mafenide' },
              { name: 'Sulfacetamida' },
              { name: 'Sulfadiazina' },
              { name: 'Sulfadoxina' },
              { name: 'Sulfametidol' }
            ]
          },
          {
            type: 'Anti-inflamatórios não esteroides',
            libs: [
              { name: 'Indometacina' },
              { name: 'Piroxicam' },
              { name: 'Ibuprofene' },
              { name: 'Cetoprofeno' },
              { name: 'Diclofenaco' }
            ]
          },
          {
            type: 'Anti-vírico',
            libs: [
              { name: 'Aciclovir' },
              { name: 'Indinavir' },
              { name: 'Foscarnet' },
              { name: 'Tenofovir' }
            ]
          },
          {
            type: 'Imuno-supressores',
            libs: [
              { name: 'Cisplatina' },
              { name: 'Metotrexato' },
              { name: 'Mitomicina C' },
              { name: 'Ifosfamida' },
              { name: 'Citumixab' }
            ]
          },
          {
            type: 'Inibidores Bomba-protões',
            libs: [
              { name: 'Omeprazol' }
            ]
          },
          {
            type: 'IECA',
            libs: [
              { name: 'Captopril' },
              { name: 'Enalapril' },
              { name: 'Lisinopril' },
              { name: 'Benazepril' },
              { name: 'Fosinopril' },
              { name: 'Quinapril' }
            ]
          },
          {
            type: 'ARA II',
            libs: [
              { name: 'Candesartana: Atacand' },
              { name: 'Candesartana: Blopress' },
              { name: 'Losartana: Aradois' },
              { name: 'Losartana: Cozaar' },
              { name: 'Losartana: Losartec' },
              { name: 'Olmesartana' },
              { name: 'Valsartana' },
              { name: 'Telmisartana' }
            ]
          },
          {
            type: 'Diuréticos - Tiazidas',
            libs: [
              { name: 'Hidroclorotiazida' },
              { name: 'Clortalidona' },
              { name: 'Indapamida: Drenol' },
              { name: 'Indapamida: Hygroton' },
              { name: 'Indapamida: Natrilix' },
              { name: 'Indapamida: Indapen' },
              { name: 'Indapamida: Fludex' },
              { name: 'Indapamida: Vasodipin' }
            ]
          },
          {
            type: 'Anti-Epileticos',
            libs: [
              { name: 'Fenitoina' }
            ]
          },
          {
            type: 'Outros',
            libs: [
              { name: 'Lítio' },
              { name: 'Manitol' },
              { name: 'Penicilina' }
            ]
          }
        ],
        valueMed: [],
        optionsDieta: [
          { name: 'Geral' },
          { name: 'Branda' },
          { name: 'Pastosa' },
          { name: 'Liquida pastosa' },
          { name: 'Liquida liquidificada' },
          { name: 'Liquida completa' },
          { name: 'Liquida sem resíduos' },
          { name: 'Hipolipidica' },
          { name: 'Hipossodica' },
          { name: 'Hipoglicidica' },
          { name: 'Hipoproteica' },
          { name: 'HAS' },
          { name: 'DM' },
          { name: 'Nefropata' },
          { name: 'Hepatopata' },
          { name: 'Cardiopata' },
          { name: 'Hiperlipidica' },
          { name: 'Hiperproteica' }
        ],
        valueDieta: [],
        tableItems: [],
        form: {
          idade: {
            value: '',
            label: 'Idade'
          },
          sexo: {
            value: 'homem',
            label: 'Sexo'
          },
          raca: {
            value: 'branco',
            label: 'Raça'
          },
          diabetes: {
            value: '',
            label: 'Diabetes'
          },
          creatinina: {
            value: '',
            label: 'Creatinina'
          },
          ureia: {
            value: '',
            label: 'Uréia'
          },
          egfr: {
            value: 0,
            label: 'eGFR'
          },
          has: {
            value: '',
            label: 'HAS'
          }
        },
        show: false
      }
    },
    watch: {
      'form.creatinina.value': function() {this.calcEgrf()},
      'form.idade.value': function() {this.calcEgrf()},
      'form.raca.value': function() {this.calcEgrf()},
      'form.sexo.value': function() {this.calcEgrf()}
    },
    filters: {
      formatNumber(num) {
        return num.toString().replace('.', ',').replace(/(\d)(?=(\d{3})+(?!\d))/g, '$1.')
      }
    },
    methods: {
      onSubmit(evt) {
        evt.preventDefault()
        
        this.doTable()
      },
      onReset(evt) {
        evt.preventDefault()

        this.form.diabetes.value = ''
        this.form.idade.value = ''
        this.form.creatinina.value = ''
        this.form.ureia.value = ''
        this.form.egfr.value = ''
        this.form.has.value = ''
        this.form.raca.value = ''
        this.form.sexo.value = ''
        this.valueMed = []
        this.valueDieta = []

        this.show = false
      },
      calcResult() {
        let result = 0;
        result += this.valueMed.length
        result += this.form.diabetes.value === 'sim' ? 1 : 0
        result += this.form.has.value === 'sim' ? 1 : 0
        result += this.form.raca.value === 'negro' ? 1 : 0
        result += this.form.sexo.value === 'homem' ? 1 : 0

        const resultObj = {
          Propriedade: 'Resultado',
          Pontuação: result,
          _rowVariant: 'info'
        }

        return resultObj
      },
      doTable() {
        this.tableItems = []
        for (const v of Object.entries(this.form)) {
          this.tableItems.push({
            Propriedade: v[1].label,
            Pontuação: v[1].value
          })
        }

        let medString = 'Total de ' + this.valueMed.length + ': '
        for (let i in this.valueMed) {
          medString += this.valueMed[i].name
          if (i < this.valueMed.length-1) medString += ', '
        }
        
        this.tableItems.push({
          Propriedade: 'Medicamentos',
          Pontuação: medString
        })

        const calcResultado = this.calcResult()
        this.tableItems.push(calcResultado)

        let resultTexto = ''
        if (calcResultado.value < 3){
          resultTexto = 'leve – mudança de prescrição médica'
        } else if (calcResultado.value < 6){
          resultTexto = 'moderado – solicitar avaliação do nefrologista'
        } else {
          resultTexto = 'alto – avaliação do nefrologista e indicação de TRS'
        }

        this.tableItems.push({
          Propriedade: 'Risco de lesão renal',
          Pontuação: resultTexto,
          _rowVariant: 'info'
        })

        this.show = true
      },
      calcEgrf() {
        if (this.form.raca.value === 'negro'){
          if (this.form.sexo.value === 'mulher') {
            if (+this.form.creatinina.value <= 0.7) {
              this.form.egfr.value = 166 * Math.pow((this.form.creatinina.value/0.7),-0.329) * Math.pow(0.993, this.form.idade.value)
            } else {
              this.form.egfr.value = 166 * Math.pow((this.form.creatinina.value/0.7),-1.209) * Math.pow(0.993, this.form.idade.value)
            }
          } else {
            if (+this.form.creatinina.value <= 0.9) {
              this.form.egfr.value = 163 * Math.pow((this.form.creatinina.value/0.9),-0.411) * Math.pow(0.993, this.form.idade.value)
            } else {
              this.form.egfr.value = 163 * Math.pow((this.form.creatinina.value/0.9),-1.209) * Math.pow(0.993, this.form.idade.value)
            }
          }
        } else {
          if (this.form.sexo.value === 'mulher') {
            if (+this.form.creatinina.value <= 0.7) {
              this.form.egfr.value = 144 * Math.pow((this.form.creatinina.value/0.7),-0.329) * Math.pow(0.993, this.form.idade.value)
            } else {
              this.form.egfr.value = 144 * Math.pow((this.form.creatinina.value/0.7),-1.209) * Math.pow(0.993, this.form.idade.value)
            }
          } else {
            if (+this.form.creatinina.value <= 0.9) {
              this.form.egfr.value = 141 * Math.pow((this.form.creatinina.value/0.9),-0.411) * Math.pow(0.993, this.form.idade.value)
            } else {
              this.form.egfr.value = 141 * Math.pow((this.form.creatinina.value/0.9),-1.209) * Math.pow(0.993, this.form.idade.value)
            }
          }
        }
        if (this.form.egfr.value === Infinity){
          this.form.egfr.value = 0
        }
      }
    }
}
</script>

<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>
<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  width: 70vw;
  color: #2c3e50;
  margin: 5vh auto;
}

.egfrresult {
  font-size: 25px;
  font-weight: bold;
}
</style>
