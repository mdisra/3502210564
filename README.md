import React from 'react';
import { BrowserRouter as Router, Routes, Route, Link } from 'react-router-dom';
import { Container, AppBar, Toolbar, Typography, Button } from '@mui/material';
import StockPage from './pages/StockPage';
import CorrelationPage from './pages/CorrelationPage';

function App() {
  return (
    <Router>
      <AppBar position="static">
        <Toolbar>
          <Typography variant="h6" sx={{ flexGrow: 1 }}>Stock Aggregator</Typography>
          <Button color="inherit" component={Link} to="/">Stock Page</Button>
          <Button color="inherit" component={Link} to="/correlation">Correlation Heatmap</Button>
        </Toolbar>
      </AppBar>
      <Container sx={{ mt: 4 }}>
        <Routes>
          <Route path="/" element={<StockPage />} />
          <Route path="/correlation" element={<CorrelationPage />} />
        </Routes>
      </Container>
    </Router>
  );
}

export default App;

import React, { useEffect, useState } from 'react';
import { Typography, Select, MenuItem, Card, CardContent, CircularProgress } from '@mui/material';
import axios from 'axios';

function StockPage() {
  const [stocks, setStocks] = useState([]);
  const [selectedStock, setSelectedStock] = useState('AAPL');
  const [interval, setInterval] = useState(30);
  const [priceData, setPriceData] = useState([]);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    axios.get('http://20.244.56.144/evaluation-service/stocks')
      .then(res => setStocks(Object.values(res.data.stocks)))
      .catch(console.error);
  }, []);

  useEffect(() => {
    if (!selectedStock) return;
    setLoading(true);
    axios.get(`http://20.244.56.144/evaluation-service/stocks/${selectedStock}?minutes=${interval}`)
      .then(res => {
        setPriceData(res.data.price);
        setLoading(false);
      })
      .catch(err => {
        console.error(err);
        setLoading(false);
      });
  }, [selectedStock, interval]);

  return (
    <Card>
      <CardContent>
        <Typography variant="h5" gutterBottom>Stock Price Viewer</Typography>

        <Select value={selectedStock} onChange={(e) => setSelectedStock(e.target.value)} sx={{ mr: 2 }}>
          {stocks.map((ticker) => (
            <MenuItem key={ticker} value={ticker}>{ticker}</MenuItem>
          ))}
        </Select>

        <Select value={interval} onChange={(e) => setInterval(Number(e.target.value))}>
          {[5, 10, 30, 60].map(m => (
            <MenuItem key={m} value={m}>{m} minutes</MenuItem>
          ))}
        </Select>

        {loading ? <CircularProgress sx={{ mt: 3 }} /> : (
          <Typography sx={{ mt: 3 }}>
            {priceData.length ? `Prices: ${priceData.join(', ')}` : 'No data available'}
          </Typography>
        )}
      </CardContent>
    </Card>
  );
}

export default StockPage;

import React from 'react';
import { Typography, Card, CardContent } from '@mui/material';

function CorrelationPage() {
  return (
    <Card>
      <CardContent>
        <Typography variant="h5" gutterBottom>Correlation Heatmap</Typography>
        <Typography>
          This page will show the correlation heatmap between stocks over the selected time interval.
          Heatmap and correlation logic to be implemented.
        </Typography>
      </CardContent>
    </Card>
  );
}

export default CorrelationPage;
