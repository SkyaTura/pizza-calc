<template lang="pug">
v-container
  v-form.my-3
    v-text-field(label="Pizza Name" outlined v-model="name")
    v-text-field(
      label="Size"
      outlined
      type="number"
      :value="size"
      @input="size = parseFloat($event)"
    )
    v-text-field(
      label="Price"
      outlined
      type="number"
      :value="price"
      @input="price = parseFloat($event)"
    )
    v-text-field(label="Price per square area" readonly outlined :value="calculate(price, size)")
    v-row.mx-0
      v-btn(text @click="clearHistory" color="red darken-2") Clear history
      v-spacer
      v-btn(text @click="clear") Clear
      v-btn(@click="save" color="blue darken-2" text) Save
  v-data-table.mt-8(
    disable-pagination
    hide-default-footer
    mobile-breakpoint="200"
    :headers="headers"
    :items="calculatedHistory"
  )
    template(#no-data)
      .black--text No pizza saved yet.
</template>

<script>
export default {
  data: () => ({
    token: 0,
    name: '',
    price: '',
    size: '',
    headers: [
      { text: 'Name', value: 'name' },
      { text: 'Price', value: 'price' },
      { text: 'Size', value: 'size' },
      { text: 'Price per cm²', value: 'calculated' },
    ],
  }),
  computed: {
    calculatedHistory() {
      return this.history.map((item) => ({
        ...item,
        calculated: this.calculate(item.price, item.size),
      }))
    },
    history: {
      get() {
        // eslint-disable-next-line no-unused-vars
        const { token } = this // this line wathces for changes in the localStorage
        return JSON.parse(localStorage.getItem('history') || '[]')
      },
      set(value) {
        this.token++
        localStorage.setItem('history', JSON.stringify(value))
        return value
      },
    },
  },
  methods: {
    calculate(price, size) {
      if (!size) return 0
      return (price / (Math.PI * Math.pow(size / 2, 2))).toFixed(4)
    },
    clear() {
      this.price = ''
      this.size = ''
      this.name = ''
    },
    clearHistory() {
      this.history = []
    },
    save() {
      const { price, size, name } = this

      this.clear()

      this.history = [...this.history, { price, size, name }]
    },
  },
}
</script>
