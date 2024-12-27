import axios from 'axios';
import * as cheerio from 'cheerio';
import { initializeApp } from 'firebase/app';
import { getFirestore, collection, addDoc, Timestamp } from 'firebase/firestore';

const firebaseConfig = {
  // COPIA AQUÍ TU CONFIGURACIÓN DE FIREBASE
    apiKey: "AIzaSyBdpTd0lS3AjJu_stSmN2s9v29Q-O9xcj8",
    authDomain: "origo-442cb.firebaseapp.com",
    projectId: "origo-442cb",
    storageBucket: "origo-442cb.firebasestorage.app",
    messagingSenderId: "798788310823",
    appId: "1:798788310823:web:54e6a1cd1b7ece06d4fb02",
    measurementId: "G-8TSE2QJZRX"
};

interface TrendingTopic {
  title: string;
  totalTweets: number;
  timestamp: Date;
}

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

async function scrapeTrends() {
  try {
    console.log('Iniciando scraping...');
    const response = await axios.get('https://www.twitter-trending.com/argentina/es');
    const $ = cheerio.load(response.data);
    
    const trends: TrendingTopic[] = [];
    
    $('.trend-card').each((i, elem) => {
      const title = $(elem).find('.trend-title').text().trim();
      const tweetsText = $(elem).find('.trend-tweets').text().trim();
      const tweets = parseInt(tweetsText.replace(/[^0-9]/g, '')) || 0;
      
      if (title && tweets) {
        trends.push({
          title,
          totalTweets: tweets,
          timestamp: new Date()
        });
      }
    });

    console.log(`Encontrados ${trends.length} trending topics`);

    // Guardar en Firestore
    for (const trend of trends) {
      await addDoc(collection(db, 'trends'), trend);
    }

    console.log('Datos guardados exitosamente en Firestore');
  } catch (error) {
    console.error('Error durante el scraping:', error);
    throw error;
  }
}

// Ejecutar el scraper
scrapeTrends()
  .then(() => {
    console.log('Scraping completado');
    process.exit(0);
  })
  .catch((error) => {
    console.error('Error en el proceso:', error);
    process.exit(1);
  });