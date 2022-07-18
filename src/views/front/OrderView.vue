<template lang="pug">
#orders
  v-row
    v-col(cols='12')
      h1.text-center 我的訂單
    v-divider
    v-col(cols='12')
      v-table
        thead
          tr
            th 編號
            th 下單日期
            th 金額
            th 商品
            th ID
        tbody
          tr(v-if='orders.length > 0' v-for='(order, i) in orders' :key='order._id')
            td {{ i + 1 }}
            td {{ new Date(order.date).toLocaleDateString() }}
            td {{ order.totalPrice }}
            td
              ul
                li(v-for='product in order.products' :key='product._id')
                  | {{ product.product.name }} x {{ product.quantity }}
            td {{ order._id }}
          tr(v-else)
            td(colspan="5")
              h3.text-center 沒有訂單
</template>

<script setup>
import { reactive } from 'vue'
import { apiAuth } from '@/plugins/axios'
import Swal from 'sweetalert2'

const orders = reactive([])

const init = async () => {
  try {
    const { data } = await apiAuth.get('/orders')
    orders.push(...data.result.map(order => {
      order.totalPrice = order.products.reduce((a, b) => {
        return a + b.product.price * b.quantity
      }, 0)
      return order
    }))
  } catch (error) {
    console.log(error)
    Swal.fire({
      icon: 'error',
      title: '失敗',
      text: '無法取得訂單'
    })
  }
}
init()
</script>
