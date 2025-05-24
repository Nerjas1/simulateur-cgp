# simulateur-cgp
// Étape 1 : fichier `vercel.json` pour configuration du déploiement automatique
{
  "version": 2,
  "builds": [
    { "src": "package.json", "use": "@vercel/next" }
  ],
  "routes": [
    { "src": "/(.*)", "dest": "/index.html" }
  ]
}

// Étape 2 : Ajout du `package.json` simplifié pour React + Tailwind + Vercel
{
  "name": "simulateur-cgp",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start"
  },
  "dependencies": {
    "next": "13.4.19",
    "react": "18.2.0",
    "react-dom": "18.2.0"
  },
  "devDependencies": {
    "tailwindcss": "^3.0.0",
    "autoprefixer": "^10.0.0",
    "postcss": "^8.0.0"
  }
}

// Étape 3 : fichier tailwind.config.js
module.exports = {
  content: [
    "./pages/**/*.{js,ts,jsx,tsx}",
    "./components/**/*.{js,ts,jsx,tsx}"
  ],
  theme: {
    extend: {},
  },
  plugins: [],
};

// Étape 4 : fichier global.css
@tailwind base;
@tailwind components;
@tailwind utilities;

// Étape 5 : fichier pages/index.tsx
import Head from 'next/head';
import { useState } from 'react';

export default function Home() {
  const [profile, setProfile] = useState("particulier");
  const [encours, setEncours] = useState(100000);
  const [services, setServices] = useState({ bilan: false, suivi: false, expert: false });
  const [resultat, setResultat] = useState(null);

  const calculerTarif = () => {
    let total = 0;
    if (services.bilan) total += profile === "entreprise" ? 3000 : 2000;
    if (services.suivi) total += encours * 0.005;
    if (services.expert) total += 5000;
    setResultat(total.toFixed(2));
  };

  return (
    <div className="max-w-xl mx-auto p-8 space-y-6">
      <Head><title>Simulateur CGP</title></Head>
      <h1 className="text-2xl font-bold text-center">Simulateur de Tarifs CGP</h1>

      <div className="space-y-4">
        <label className="block">Profil</label>
        <select className="w-full border rounded p-2" value={profile} onChange={(e) => setProfile(e.target.value)}>
          <option value="particulier">Particulier</option>
          <option value="entreprise">Chef d'entreprise</option>
          <option value="liberal">Profession libérale</option>
        </select>

        <label className="block">Encours estimé (€)</label>
        <input type="range" min="10000" max="1000000" step="10000" value={encours} onChange={(e) => setEncours(Number(e.target.value))} className="w-full" />
        <p className="text-sm">{encours.toLocaleString()} €</p>

        <div className="space-y-2">
          <label className="block font-semibold">Services souhaités</label>
          <label className="flex items-center space-x-2">
            <input type="checkbox" checked={services.bilan} onChange={(e) => setServices({ ...services, bilan: e.target.checked })} />
            <span>Bilan patrimonial initial</span>
          </label>
          <label className="flex items-center space-x-2">
            <input type="checkbox" checked={services.suivi} onChange={(e) => setServices({ ...services, suivi: e.target.checked })} />
            <span>Suivi patrimonial annuel</span>
          </label>
          <label className="flex items-center space-x-2">
            <input type="checkbox" checked={services.expert} onChange={(e) => setServices({ ...services, expert: e.target.checked })} />
            <span>Mission spécifique (expert)</span>
          </label>
        </div>

        <button onClick={calculerTarif} className="w-full bg-blue-600 text-white p-2 rounded mt-4">Calculer mon tarif</button>

        {resultat && (
          <div className="mt-4 text-center">
            <p className="text-xl font-semibold">Tarif estimé : {parseFloat(resultat).toLocaleString()} € HT</p>
            <p className="text-sm text-gray-500">(hors TVA – estimation indicative)</p>
          </div>
        )}
      </div>
    </div>
