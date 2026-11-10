<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>
          <span class="title-content">
            <img :src="cryptoIcon" alt="" class="title-icon" />
            <span>Crypto Market</span>
          </span>
        </ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true" class="ion-padding">
      <div class="button-container">
        <ion-button @click="fetchCryptoData" color="primary">
          Refresh
        </ion-button>
      </div>

      <ion-list lines="none" class="crypto-list">
        <ion-item v-for="coin in coins" :key="coin.id" class="crypto-item">
          <ion-grid>
            <ion-row class="ion-align-items-center">
              
              <!-- Kolom Rank (Mengecil) -->
              <ion-col size="1.5" class="ion-text-center">
                <div class="label-text">Rank</div>
                <div class="value-text">{{ coin.rank }}</div>
              </ion-col>

              <!-- Kolom Name & Symbol -->
              <ion-col size="3.5" class="ion-text-left">
                <div class="label-text">{{ coin.name }}</div>
                <div class="value-text">{{ coin.symbol }}</div>
              </ion-col>

              <!-- Kolom USD Price (Ditambah indikator warna) -->
              <ion-col size="3.5" class="ion-text-left">
                <div class="label-text">USD</div>
                <div class="value-text" :class="Number(coin.percent_change_24h) >= 0 ? 'text-green' : 'text-red'">
                  {{ coin.price_usd }}
                </div>
              </ion-col>

              <!-- Kolom Baru: Graph -->
              <ion-col size="3.5">
                <SparklineChart 
                  :history="coin.history" 
                  :isPositive="Number(coin.percent_change_24h) >= 0" 
                />
              </ion-col>

            </ion-row>
          </ion-grid>
        </ion-item>
      </ion-list>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { 
  IonPage, IonHeader, IonToolbar, IonTitle, IonContent, 
  IonButton, IonList, IonItem, IonGrid, IonRow, IonCol 
} from '@ionic/vue';
import axios from 'axios';
import SparklineChart from '@/components/SparklineChart.vue'; // Import komponen grafik
import cryptoIcon from '../../Source IMG/cryptocurrencies.png';

// Interface diperbarui untuk menampung history dummy dan percent change
interface CoinData {
  id: string;
  rank: number;
  name: string;
  symbol: string;
  price_usd: string;
  percent_change_24h: string;
  history: number[]; // Array untuk data grafik
}

const coins = ref<CoinData[]>([]);

// Fungsi membuat array data acak untuk mensimulasikan grafik
const generateDummyHistory = (currentPrice: number): number[] => {
  const history = [];
  for (let i = 0; i < 7; i++) {
    // Membuat fluktuasi harga acak +/- 5% dari harga saat ini
    const fluctuation = currentPrice * (Math.random() * 0.1 - 0.05);
    history.push(currentPrice + fluctuation);
  }
  history.push(currentPrice); // Titik terakhir adalah harga asli saat ini
  return history;
};

const fetchCryptoData = async () => {
  try {
    const response = await axios.get('https://api.coinlore.net/api/tickers/');
    
    // Mapping data API untuk menyuntikkan history dummy ke dalam state
    coins.value = response.data.data.map((coin: any) => ({
      ...coin,
      history: generateDummyHistory(Number(coin.price_usd))
    }));

  } catch (error) {
    console.error('Terjadi kesalahan saat mengambil data API:', error);
  }
};

onMounted(() => {
  fetchCryptoData();
});
</script>

<style scoped>
.title-content {
  display: inline-flex;
  align-items: center;
  gap: 8px;
}

.title-icon {
  width: 28px;
  height: 28px;
  object-fit: contain;
}

.button-container {
  display: flex;
  justify-content: center;
  margin-top: 10px;
  margin-bottom: 20px;
}

.crypto-list {
  background: transparent;
  padding: 0;
}

.crypto-item {
  --background: #191918;
  --border-color: #f8f7f4;
  --border-width: 0 0 1px 0;
  --border-style: solid;
  --padding-start: 0;
  --inner-padding-end: 0;
  margin-bottom: 0;
}

.label-text {
  font-size: 0.70rem;
  color: #f8f8f8;
  margin-bottom: 2px;
}

.value-text {
  font-size: 1.1rem;
  font-weight: bold;
  color: #fafafa;
  /* Mencegah teks terpotong */
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis; 
}

/* Warna dinamis untuk harga */
.text-green { color: #1e8736; }
.text-red { color: #dc3545; }

.ion-text-left {
  padding-left: 5px;
}
</style>
