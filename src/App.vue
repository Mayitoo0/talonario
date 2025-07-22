<template>
  <div class="modal fade" id="raffleModal" tabindex="-1" aria-labelledby="raffleModalLabel" aria-hidden="true" data-bs-backdrop="static">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="raffleModalLabel">Configuración de la Rifa</h5>
        </div>
        <div class="modal-body">
          <form @submit.prevent="saveRaffleData">
            <div class="mb-3">
              <label for="prizeValue" class="form-label">Precio del premio</label>
              <input type="number" class="form-control" id="prizeValue" v-model="raffleData.prizeValue" required>
            </div>
            <div class="mb-3">
              <label for="ticketPrice" class="form-label">Precio de la boleta</label>
              <input type="number" class="form-control" id="ticketPrice" v-model="raffleData.ticketPrice" required>
            </div>
            <div class="mb-3">
              <label for="lotteryDate" class="form-label">Fecha de la rifa</label>
              <input type="date" class="form-control" id="lotteryDate" v-model="raffleData.lotteryDate" required>
            </div>
            <div class="mb-3">
              <label for="lottery" class="form-label">Lotería</label>
              <select class="form-select" id="lottery" v-model="raffleData.lottery" required>
                <option value="" disabled selected>Seleccione una lotería</option>
                <option v-for="(lottery, index) in lotteries" :key="index" :value="lottery">{{ lottery }}</option>
              </select>
            </div>
            <div class="mb-3">
              <label for="ticketCount" class="form-label">Cantidad de boletas</label>
              <select class="form-select" id="ticketCount" v-model="raffleData.ticketCount" required>
                <option value="100">100 boletas</option>
                <option value="200">200 boletas</option>
              </select>
            </div>
            <div class="modal-footer">
              <button type="submit" class="btn btn-primary">Guardar</button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>

  <div class="modal fade" id="ticketModal" tabindex="-1" aria-labelledby="ticketModalLabel" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="ticketModalLabel">Boleta #{{ currentTicket.number }}</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <form @submit.prevent="saveTicketInfo">
            <div class="mb-3">
              <label for="buyerName" class="form-label">Nombre del comprador</label>
              <input type="text" class="form-control" id="buyerName" v-model="currentTicket.buyerName" required>
            </div>
            <div class="mb-3">
              <label for="paymentStatus" class="form-label">Estado de pago</label>
              <select class="form-select" id="paymentStatus" v-model="currentTicket.paymentStatus" required>
                <option value="paid">Pagado</option>
                <option value="pending">Pendiente de pago</option>
              </select>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancelar</button>
              <button type="submit" class="btn btn-primary">Guardar</button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>

  <div class="modal fade" id="ticketsListModal" tabindex="-1" aria-labelledby="ticketsListModalLabel" aria-hidden="true">
    <div class="modal-dialog modal-xl">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="ticketsListModalLabel">Listado de Boletas</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <div class="row mb-4">
            <div class="col-md-3">
              <div class="card bg-light">
                <div class="card-body">
                  <h6 class="card-title">Total Boletas</h6>
                  <p class="card-text display-6">{{ raffleData.ticketCount }}</p>
                </div>
              </div>
            </div>
            <div class="col-md-3">
              <div class="card bg-light">
                <div class="card-body">
                  <h6 class="card-title">Vendidas</h6>
                  <p class="card-text display-6">{{ soldTicketsCount }}</p>
                </div>
              </div>
            </div>
            <div class="col-md-3">
              <div class="card bg-light">
                <div class="card-body">
                  <h6 class="card-title">Disponibles</h6>
                  <p class="card-text display-6">{{ availableTicketsCount }}</p>
                </div>
              </div>
            </div>
            <div class="col-md-3">
              <div class="card bg-light">
                <div class="card-body">
                  <h6 class="card-title">Recaudado</h6>
                  <p class="card-text display-6">${{ formatNumber(totalCollected) }}</p>
                </div>
              </div>
            </div>
          </div>
          
          <table class="table table-striped table-hover">
            <thead>
              <tr>
                <th>Número</th>
                <th>Estado</th>
                <th>Comprador</th>
                <th>Pago</th>
                <th>Valor</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="ticket in tickets.filter(t => t.sold)" :key="ticket.number">
                <td>{{ ticket.number }}</td>
                <td>
                  <span class="badge" :class="{
                    'bg-paid': ticket.paymentStatus === 'paid',
                    'bg-pending': ticket.paymentStatus === 'pending'
                  }">
                    {{ ticket.paymentStatus === 'paid' ? 'Pagado' : 'Pendiente' }}
                  </span>
                </td>
                <td>{{ ticket.buyerName }}</td>
                <td>{{ ticket.paymentStatus === 'paid' ? 'Pagado' : 'Pendiente' }}</td>
                <td>${{ formatNumber(raffleData.ticketPrice) }}</td>
              </tr>
              <tr v-if="tickets.filter(t => t.sold).length === 0">
                <td colspan="5" class="text-center text-muted">No hay boletas vendidas aún.</td>
              </tr>
            </tbody>
          </table>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cerrar</button>
        </div>
      </div>
    </div>
  </div>

  <div class="modal fade" id="customizeModal" tabindex="-1" aria-labelledby="customizeModalLabel" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="customizeModalLabel">Personalizar Apariencia</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <div class="mb-3">
            <label class="form-label">Tema de color</label>
            <div class="d-flex flex-wrap gap-2">
              <button v-for="theme in themes" :key="theme.name" 
                class="btn" 
                :style="{
                  backgroundColor: theme.colors.primary,
                  color: theme.textColor
                }"
                @click="applyTheme(theme)"
              >
                {{ theme.name }}
              </button>
            </div>
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancelar</button>
        </div>
      </div>
    </div>
  </div>

  <div class="main-container" v-if="raffleConfigured" :style="currentThemeStyle">
    <div class="info-panel">
      <h3>Información de la Rifa</h3>
      <p><strong>Premio:</strong> ${{ formatNumber(raffleData.prizeValue) }}</p>
      <p><strong>Precio boleta:</strong> ${{ formatNumber(raffleData.ticketPrice) }}</p>
      <p><strong>Fecha de juego:</strong> {{ raffleData.lotteryDate }}</p>
      <p><strong>Lotería:</strong> {{ raffleData.lottery }}</p>
      <p><strong>Total boletas:</strong> {{ raffleData.ticketCount }}</p>
      
      <button class="btn btn-secondary mt-3" @click="resetRaffle">Reiniciar Rifa</button>
    </div>
    <div class="content-flex">
      <div class="tickets-container">
        <button 
          v-for="ticket in tickets" 
          :key="ticket.number"
          class="ticket-btn" 
          :class="getTicketClass(ticket)"
          @click="openTicketModal(ticket)"
        >
          {{ ticket.number }}
          <span v-if="ticket.buyerName" class="ticket-badge">
            {{ ticket.paymentStatus === 'paid' ? '✓' : '!' }}
          </span>
        </button>
      </div>
      
      <div class="action-buttons">
        <button class="btn btn-info mb-2" @click="openTicketsList">
          <i class="bi bi-list-ul"></i> Listar Boletas
        </button>
        <button class="btn btn-warning" @click="openCustomizeModal">
          <i class="bi bi-palette"></i> Personalizar
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, computed } from 'vue'

export default {
  setup() {
    const raffleModal = ref(null)
    const ticketModal = ref(null)
    const ticketsListModal = ref(null)
    const customizeModal = ref(null)
    const raffleConfigured = ref(false)
    
    const raffleData = ref({
      prizeValue: 0,
      ticketPrice: 0,
      lotteryDate:'',
      lottery: '',
      ticketCount: '100'
    })
    
    const currentTicket = ref({
      number: 0,
      buyerName: '',
      paymentStatus: 'paid',
      sold: false
    })
    
    const tickets = ref([])
    const lotteries = ref([
      'Lotería de Bogotá',
      'Lotería de Medellín',
      'Lotería de Cali',
      'Lotería de Barranquilla',
      'Lotería Nacional'
    ])
    
    const themes = ref([
      {
        name: 'Arandano',
        colors: {
          primary: '#30364a',
          secondary: '#111016',
          available: '#757b92',
          paid: '#d2a283',
          pending: '#c4d0ee'
        },
        textColor: 'white',
      },
      {
        name: 'Sandia',
        colors: {
          primary: '#24e0b8',
          secondary: '#007b00',
          available: '#ff3031',
          paid: '#24e0b8',
          pending: '#ffcc51'
        },
        textColor: 'white'
      },
      {
        name: 'Limon',
        colors: {
          primary: '#ffd500',
          secondary: '#043007',
          available: '#005c13',
          paid: '#e59800',
          pending: '#e2e2eb'
        },
        textColor: 'black'
      },
      {
        name: 'Fresa',
        colors: {
          primary: '#df0000',
          secondary: '#4a0000',
          available: '#ffa5a9',
          paid: '#2a5800',
          pending: '#cad15e'
        },
        textColor: 'white'
      }
    ])
    
    const currentTheme = ref(themes.value[0])
    const currentThemeStyle = computed(() => ({
      '--primary-color': currentTheme.value.colors.primary,
      '--secondary-color': currentTheme.value.colors.secondary,
      '--available-color': currentTheme.value.colors.available,
      '--paid-color': currentTheme.value.colors.paid,
      '--pending-color': currentTheme.value.colors.pending
    }))
    
    onMounted(() => {
      raffleModal.value = new bootstrap.Modal(document.getElementById('raffleModal'))
      ticketModal.value = new bootstrap.Modal(document.getElementById('ticketModal'))
      ticketsListModal.value = new bootstrap.Modal(document.getElementById('ticketsListModal'))
      customizeModal.value = new bootstrap.Modal(document.getElementById('customizeModal'))
      raffleModal.value.show()
    })
    
    const saveRaffleData = () => {
      raffleConfigured.value = true
      initializeTickets()
      raffleModal.value.hide()
    }
    
    const initializeTickets = () => {
      const count = parseInt(raffleData.value.ticketCount)
      tickets.value = Array.from({ length: count }, (_, i) => ({
        number: i + 1,
        buyerName: '',
        paymentStatus: '',
        sold: false
      }))
    }
    
    const openTicketModal = (ticket) => {
      currentTicket.value = { ...ticket }
      ticketModal.value.show()
    }
    
    const openTicketsList = () => {
      ticketsListModal.value.show()
    }
    
    const openCustomizeModal = () => {
      customizeModal.value.show()
    }
    
    const saveTicketInfo = () => {
      const index = tickets.value.findIndex(t => t.number === currentTicket.value.number)
      if (index !== -1) {
        tickets.value[index] = {
          ...currentTicket.value,
          sold: true
        }
      }
      ticketModal.value.hide()
    }
    
    const applyTheme = (theme) => {
      currentTheme.value = theme
    }
    
    const resetRaffle = () => {
      if (confirm('¿Está seguro que desea reiniciar la rifa? Se perderán todos los datos.')) {
        raffleConfigured.value = false
        raffleData.value = {
          prizeValue: 0,
          ticketPrice: 0,
          lottery: '',
          ticketCount: '100'
        }
        tickets.value = []
        raffleModal.value.show()
      }
    }
    
    const formatNumber = (num) => {
      return num ? num.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".") : '0'
    }
    
    const getTicketClass = (ticket) => {
      if (!ticket.sold) return 'available'
      return ticket.paymentStatus === 'paid' ? 'paid' : 'pending'
    }
    
    const soldTicketsCount = computed(() => {
      return tickets.value.filter(t => t.sold).length
    })
    
    const availableTicketsCount = computed(() => {
      return tickets.value.length - soldTicketsCount.value
    })
    
    const paidTicketsCount = computed(() => {
      return tickets.value.filter(t => t.paymentStatus === 'paid' && t.sold).length
    })
    
    const pendingTicketsCount = computed(() => {
      return tickets.value.filter(t => t.paymentStatus === 'pending' && t.sold).length
    })
    
    const totalCollected = computed(() => {
      return paidTicketsCount.value * raffleData.value.ticketPrice
    })
    
    return {
      raffleModal,
      ticketModal,
      ticketsListModal,
      customizeModal,
      raffleConfigured,
      raffleData,
      currentTicket,
      tickets,
      lotteries,
      themes,
      currentTheme,
      currentThemeStyle,
      saveRaffleData,
      openTicketModal,
      openTicketsList,
      openCustomizeModal,
      saveTicketInfo,
      applyTheme,
      resetRaffle,
      formatNumber,
      getTicketClass,
      soldTicketsCount,
      availableTicketsCount,
      paidTicketsCount,
      pendingTicketsCount,
      totalCollected
    }
  }
}
</script>
<style scoped>
:root {
  --primary-color: #007bff;
  --secondary-color: #6c757d;
}

.main-container {
  display: flex;
  min-height: 100vh;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  position: relative;
}

.info-panel {
  width: 300px;
  background-color: #f8f9fa;
  padding: 20px;
  border-right: 1px solid #dee2e6;
  box-shadow: 2px 0 5px rgba(0,0,0,0.1);
}

.content-flex {
  display: flex;
  flex-direction: row;
  flex: 1;
  min-height: 100vh;
  position: relative;
}

.tickets-container {
  flex: 1;
  padding: 30px 20px 20px 20px;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(70px, 1fr));
  gap: 15px;
  align-content: flex-start;
  background-color: #f5f5f5;
  overflow-y: auto;
  height: 100vh;
}

.action-buttons {
  display: flex;
  flex-direction: column;
  gap: 15px;
  align-items: flex-end;
  justify-content: flex-start;
  padding: 30px 20px 0 0;
  min-width: 180px;
  position: static;
}

.ticket-btn {
  width: 70px;
  height: 70px;
  font-size: 18px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 10px;
  border: none;
  cursor: pointer;
  transition: all 0.3s ease;
  position: relative;
  font-weight: bold;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

.ticket-btn:hover {
  transform: translateY(-3px);
  box-shadow: 0 4px 8px rgba(0,0,0,0.15);
}

.ticket-btn:active {
  transform: translateY(1px);
}

.available {
  background-color: var(--available-color);
  color: white;
}

.paid {
  background-color: var(--paid-color);
  color: white;
}

.pending {
  background-color: var(--pending-color);
  color: #212529;
}

.ticket-badge {
  position: absolute;
  bottom: 5px;
  right: 5px;
  background-color: rgba(255, 255, 255, 0.8);
  border-radius: 50%;
  width: 20px;
  height: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 12px;
  color: #000;
}

.badge {
  display: inline-block;
  width: 12px;
  height: 12px;
  border-radius: 50%;
  padding: 0;
}

.bg-available {
  background-color: var(--available-color);
}

.bg-paid {
  background-color: var(--paid-color);
}

.bg-pending {
  background-color: var(--pending-color);
}

.modal-content {
  border-radius: 15px;
  overflow: hidden;
}

.modal-header {
  background-color: #f8f9fa;
  border-bottom: 1px solid #dee2e6;
}

.form-control, .form-select {
  border-radius: 8px;
  padding: 10px 15px;
}

.btn {
  border-radius: 8px;
  padding: 8px 20px;
  font-weight: 500;
  transition: all 0.2s;
}

.btn-primary {
  background-color: var(--primary-color);
  border-color: var(--primary-color);
}

.btn-secondary {
  background-color: var(--secondary-color);
  border-color: var(--secondary-color);
}

.btn-info {
  background-color: #17a2b8;
  border-color: #17a2b8;
  color: white;
}

.btn-warning {
  background-color: #ffc107;
  border-color: #ffc107;
  color: #212529;
}

.btn-primary:hover, 
.btn-secondary:hover,
.btn-info:hover,
.btn-warning:hover {
  opacity: 0.9;
  transform: translateY(-1px);
}

.info-panel h3 {
  color: #343a40;
  border-bottom: 2px solid var(--primary-color);
  padding-bottom: 10px;
  margin-bottom: 20px;
}

.info-panel p {
  margin-bottom: 10px;
  color: #495057;
}

.info-panel strong {
  color: #212529;
}

.table {
  margin-top: 20px;
}

.table th {
  background-color: var(--primary-color);
  color: white;
}

.table-hover tbody tr:hover {
  background-color: rgba(0, 123, 255, 0.1);
}

.form-control-color {
  width: 50px;
  height: 50px;
  padding: 2px;
  border-radius: 50%;
}

.form-control-color::-webkit-color-swatch {
  border-radius: 50%;
  border: none;
}

.bi {
  margin-right: 5px;
}
</style>