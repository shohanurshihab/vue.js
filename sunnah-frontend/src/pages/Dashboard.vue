<script>
import { ref, onMounted } from 'vue'
import axios from 'axios'
import Chart from 'chart.js/auto'
export default {
  setup() {
    const users = ref(0)
    const orders = ref(0)
    const product = ref(0)
    
    const fetchUsers = () => {
      axios.get(`/api/users`)
        .then(response => users.value = response.data.length)
        .catch(error => console.log(error.response))
    }

    const fetchOrders = () => {
      axios.get(`/api/orders`)
        .then(response => orders.value = response.data.length)
        .catch(error => console.log(error.response))
    }

    const fetchProduct = () => {
      axios.get(`/api/products`)
        .then(response => product.value = response.data.length)
        .catch(error => console.log(error.response))
    }
    
  
  onMounted(() => {
      Promise.all([fetchUsers(), fetchOrders(), fetchProduct()])
        .then(() => {
          const ctx = document.getElementById('orders-chart').getContext('2d');
          new Chart(ctx, {
            type: 'bar',
            data: {
              labels: ['Users', 'Orders', 'Products'],
              datasets: [{
                label: 'Count',
                data: [10, 2, 12],
                backgroundColor: [
                  'rgba(255, 99, 132, 0.2)',
                  'rgba(54, 162, 235, 0.2)',
                  'rgba(255, 206, 86, 0.2)'
                ],
                borderColor: [
                  'rgba(255, 99, 132, 1)',
                  'rgba(54, 162, 235, 1)',
                  'rgba(255, 206, 86, 1)'
                ],
                borderWidth: 3
              }]
            },
            options: {
              scales: {
                y: {
                  beginAtZero: true
                }
              }
            }
          });
        })
        .catch(error => console.log(error))
    })
    
 
    return {
      users,
      orders,
      product
    }
  }
}


</script>

<template>
  <div class="dashboard">
    <div class="bars">
      <div class="bar">
        <h2>Users</h2>
        <div class="bar-value">{{ users }}</div>
      </div>
      <div class="bar">
        <h2>Orders</h2>
        <div class="bar-value">{{ orders }}</div>
      </div>
      <div class="bar">
        <h2>Products</h2>
        <div class="bar-value">{{ product }}</div>
      </div>
    </div>
    <div class="chart" id="chart">
      <canvas id="orders-chart"></canvas>
    </div>
  </div>
</template>


<style>
.dashboard {
  padding: 1rem;
}

.bars {
  display: flex;
  justify-content: space-between;
  margin-bottom: 2rem;
}

.bar {
  flex-basis: calc(33.33% - 1rem);
  background-color: #eee;
  border-radius: 5px;
  padding: 1rem;
  box-sizing: border-box;
  text-align: center;
}

.bar h2 {
  margin: 0;
  font-size: 1.5rem;
  text-align: center;
}

.bar-value {
  font-size: 2rem;
  font-weight: bold;
  text-align: center;
}

.chart {
  width: 100%;
  max-width: 800px;
  height: 400px;
  margin: 1rem 0;
  background-color: #f0f0f0;
  border-radius: 5px;
  overflow: hidden;
}

@media (max-width: 600px) {
  .bars {
    flex-wrap: wrap;
  }

  .bar {
    flex-basis: calc(100% - 2rem);
    margin-bottom: 1rem;
  }

  .chart {
    height: 300px;
  }
}
</style>
