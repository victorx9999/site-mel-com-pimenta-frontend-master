<template>
  <b-card>
    <nuxt ref="page"/>
    <custom-loading v-if="responseState.isLoading"></custom-loading>
    <div slot="header">
      <strong>Cadastro</strong> de Produtos
    </div>
    <b-form-group
      id="fieldset-1"
      label="Nome do Produto"
      label-for="input-name"
      :state="$v.name.$dirty ? !$v.name.$error : null"
    >
      <b-form-input aria-describedby="input-name-live-feedback" required size='lg' id="input-name" v-model="$v.name.$model" :state="$v.name.$dirty ? !$v.name.$error : null" trim></b-form-input>
      <b-form-invalid-feedback id="input-name-live-feedback">
        Este campo é obrigatório e com no mínimo 3 letras.
      </b-form-invalid-feedback>
    </b-form-group>

    <b-row>
      <b-col>
        <b-form-group
        label-for="category"
        label="Selecione a Categoria"
        >
          <b-form-select
          :state="$v.categorySelected.$dirty ? !$v.categorySelected.$error : null"
          @change.native="fetchSubcategoryOptions($event)"
          id="category"
          size='lg'
          v-model="$v.categorySelected.$model"
          :options="categoryOptions">
          </b-form-select>
          <b-form-invalid-feedback>
            Este campo é obrigatório
          </b-form-invalid-feedback>
        </b-form-group>
      </b-col>
      <b-col>
        <b-form-group
        label-for="subcategory"
        label="Selecione a Subcategoria"
        >
          <b-form-select
          :state="$v.subcategorySelected.$dirty ? !$v.subcategorySelected.$error : null"
          id="subcategory"
          size='lg'
          v-model="$v.subcategorySelected.$model"
          :options="subcategoryOptions">
          </b-form-select>
          <b-form-invalid-feedback>
            Este campo é obrigatório
          </b-form-invalid-feedback>
        </b-form-group>
      </b-col>
    </b-row>

    <b-row>
      <b-col>
        <b-form-group
        label-for="amount"
        label="Informe a Quantidade"
        description="Quantidade em estoque."
        >
          <b-form-input
          :state="$v.amount.$dirty ? !$v.amount.$error : null"
          id="amount" size='lg'
          type="number"
          v-model="$v.amount.$model"
          placeholder="Quantidade">
          </b-form-input>
          <b-form-invalid-feedback>
            Este campo é obrigatório
          </b-form-invalid-feedback>
        </b-form-group>
      </b-col>
      <b-col>
        <b-form-group
        label-for="gross-price"
        label="Informe o Preço Bruto"
        description="Preço bruto do produto."
        >
          <b-form-input :state="$v.grossprice.$dirty ? !$v.grossprice.$error : null"
          id="gross-price"
          size='lg'
          type="number"
          v-model="$v.grossprice.$model"
          placeholder="Preço Bruto">
          </b-form-input>
          <b-form-invalid-feedback>
            Este campo é obrigatório
          </b-form-invalid-feedback>
        </b-form-group>
      </b-col>
      <b-col>
        <b-form-group
        label-for="discount"
        label="Informe o Desconto"
        description="Caso haja desconto, gerará automaticamente o preço líquido do produto."
        >
          <b-form-input id="discount" size='lg' type="number" v-model="discount" placeholder="Desconto"></b-form-input>
        </b-form-group>
      </b-col>
    </b-row>

    <b-form-group
    v-if="this.categorySelected == 'Moda Íntima'"
    label-for="checkbox-group-1"
    label="Informe os Tamanhos"
    >
      <b-form-checkbox-group
        id="checkbox-group-1"
        v-model="sizeSelected"
        :options="sizeOptions"
        name="size"
      ></b-form-checkbox-group>
      <div class="mt-3">Selecionados: <strong>{{ sizeSelected }}</strong></div>
    </b-form-group>

    <b-form-group
    v-if="this.categorySelected == 'Sex Shop'"
    label-for="checkbox-group-2"
    label="Informe os Sabores"
    >
      <b-form-checkbox-group
        id="checkbox-group-2"
        v-model="flavorSelected"
        :options="flavorOptions"
        name="flavor"
      ></b-form-checkbox-group>
      <div class="mt-3">Selecionados: <strong>{{ flavorSelected }}</strong></div>
    </b-form-group>

    <b-form-group
    v-if="this.categorySelected == 'Moda Íntima'"
    label-for="color"
    label="Informe as Cores"
    >
      <no-ssr>
        <vue-tags-input
          id="color"
          v-model="color"
          :tags="colors"
          @tags-changed="newTags => colors = newTags"
        />
      </no-ssr>
      <div class="mt-3">Cores: <strong>{{ colors }}</strong></div>
    </b-form-group>

    <b-form-group
    label-for="textarea"
    label="Descrição do Produto"
    >
      <b-form-textarea
        :state="$v.text.$dirty ? !$v.text.$error : null"
        size='lg'
        id="textarea"
        v-model="$v.text.$model"
        placeholder="Descrição..."
        rows="3"
        max-rows="6"
      ></b-form-textarea>
      <b-form-invalid-feedback>
        Este campo é obrigatório
      </b-form-invalid-feedback>
    </b-form-group>

    <b-form-group>
      <b-form-file
      size='lg'
      v-model="file"
      :state="Boolean(file)"
      placeholder="Escolha o Arquivo ou Arraste-o até Aqui"
      drop-placeholder="Arraste o arquivo até Aqui..."
      @change.native="onFileSelected($event)"
      ></b-form-file>
    </b-form-group>

    <div slot="footer">
      <b-button @click="store" type="submit" size="lg" variant="primary"><i class="fa fa-dot-circle-o"></i> Submeter</b-button>
      <b-button @click="resetFields" type="reset" size="md" variant="danger"><i class="fa fa-ban"></i> Resetar</b-button>
    </div>
  </b-card>
</template>

<script>
  import { required, minLength } from 'vuelidate/lib/validators'
  export default {
    computed: {

    },
    data() {
      return {
        responseState: {
          message: '',
          error: '',
          isLoading: false
        },
        responseImage: '',
        color: '',
        colors: [],
        colorsSelected: [],
        name: '',
        amount: null,
        grossprice: null,
        discount: null,
        file: null,
        text: '',
        categorySelected: null,
        categoryOptions: [
          { value: null, text: 'Selecione uma opção de Categoria' },
          { value: 'Moda Íntima', text: 'Moda Íntima' },
          { value: 'Sex Shop', text: 'Sex Shop' }
        ],
        subcategoryOptions: [],
        subcategorySelected: null,
        sizeSelected: [], // Must be an array reference!
        sizeOptions: [
          { text: 'P', value: 'P' },
          { text: 'M', value: 'M' },
          { text: 'G', value: 'G' },
          { text: 'GG', value: 'GG' },
          { text: '38', value: '38' },
          { text: '39', value: '39' },
          { text: '40', value: '40' },
          { text: '41', value: '41' },
          { text: '42', value: '42' },
          { text: '43', value: '43' },
          { text: '44', value: '44' },
          { text: '45', value: '45' },
          { text: '46', value: '46' },
          { text: '47', value: '47' },
          { text: '48', value: '48' },
          { text: '49', value: '49' },
          { text: '50', value: '50' }
        ],
        flavorSelected: [], // Must be an array reference!
        flavorOptions: [
          { text: 'Morango', value: 'Morango' },
          { text: 'Chocolate', value: 'Chocolate' },
          { text: 'Tutti frutti', value: 'Tutti frutti' },
          { text: 'Uva', value: 'Uva' },
          { text: 'Melancia', value: 'Melancia' },
          { text: 'Menta', value: 'Menta' }
        ]
      }
    },
    validations: {
      name: { required, minLength: minLength(3) },
      amount: { required },
      grossprice: { required },
      file: { required },
      text: { required },
      categorySelected: { required },
      subcategorySelected: { required }
      // rules object
    },
    methods: {
      fetchSubcategoryOptions(event){
        if(event.target.value == 'Moda Íntima'){
          this.subcategoryOptions = [
          { value: null, text: 'Selecione uma opção de Subcategoria' },
          { value: 'Calcinhas básicas', text: 'Calcinhas básicas' },
          { value: 'Calcinhas fio dental', text: 'Calcinhas fio dental' },
          { value: 'Sutiãs', text: 'Sutiãs' },
          { value: 'Conjunto lingerie', text: 'Conjunto lingerie' },
          { value: 'Camisolas', text: 'Camisolas' },
          { value: 'Baby doll', text: 'Baby doll' }
          ]
        }
        else if(event.target.value == 'Sex Shop'){
          this.subcategoryOptions = [
          { value: null, text: 'Selecione uma opção de Subcategoria' },
          { value: 'Lubrificantes', text: 'Lubrificantes' },
          { value: 'Pomadas', text: 'Pomadas' },
          { value: 'Comestíveis', text: 'Comestíveis' },
          { value: 'Brincadeiras eróticas', text: 'Brincadeiras eróticas' },
          { value: 'Vibradores', text: 'Vibradores' }
          ]
        }
        else
           this.subcategoryOptions = []
      },
      onFileSelected(event) {
        this.file = event.target.files[0];
        console.log(this.file)
      },
      async store () {
        if(!this.$v.$invalid){
          if (this.responseState.isLoading) {
            return false
          }
          this.responseState.isLoading = true

          for (const color of this.colors) {
            this.colorsSelected.push(color.text)
          }
          const fd = new FormData();
          fd.append('image_product', this.file, this.file.name)
          try {
            const responseImage  = await this.$axios.post('/product/saveimage',
              fd,
              {
              headers: {
                'Content-Type': 'multipart/form-data'
              }
            })

            this.responseImage = responseImage.data.fileNameToStore
          } catch (e) {
            this.responseState = { error: e, errorText: 'Não foi possível enviar a imagem', isLoading: false }
            console.log(e.response.data)
          }

          try {
            const { data } = await this.$axios.post('/product',
              {
                name: this.name,
                gross_price: this.grossprice,
                discount: this.discount,
                amount: this.amount,
                fileNameToStore: this.responseImage,
                description: this.text,
                color: this.colorsSelected.sort().join(),
                size: this.sizeSelected.sort().join(),
                flavor: this.flavorSelected.sort().join(),
                category: this.categorySelected,
                subcategory: this.subcategorySelected
              })
              console.dir(data)
              this.responseState.message = data.message
          } catch (e) {
            this.responseState.error = e
            this.responseState.isLoading = false
          }
          // setTimeout(function(){ this.loading = false }, 3000);
          this.responseState.isLoading = false

          document.location.reload(true);
        }
        else {
          this.$v.$touch()
        }
      },
      resetFields() {
        this.name = ''
        this.color = ''
        this.colors = []
        this.colorsSelected = []
        this.amount = null
        this.grossprice = null
        this.discount = null
        this.file = null
        this.text = ''
        this.categorySelected = null
        this.subcategorySelected = null
        this.sizeSelected = []
        this.flavorSelected = []
      }
    }
  };
</script>
