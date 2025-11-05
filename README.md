# base.ethimport ccxt
import pandas as pd
import time

# Khởi tạo Binance (thay API key thật khi dùng)
exchange = ccxt.binance({
    'apiKey': 'YOUR_API_KEY',
    'secret': 'YOUR_SECRET',
})

def get_data(symbol='BTCUSDT', timeframe='1h', limit=100):
    ohlcv = exchange.fetch_ohlcv(symbol, timeframe, limit=limit)
    df = pd.DataFrame(ohlcv, columns=['timestamp', 'open', 'high', 'low', 'close', 'volume'])
    df['timestamp'] = pd.to_datetime(df['timestamp'], unit='ms')
    return df

def sma_crossover_strategy():
    df = get_data()
    df['sma_short'] = df['close'].rolling(10).mean()  # SMA 10
    df['sma_long']  = df['close'].rolling(30).mean()  # SMA 30

    # Tín hiệu
    if df['sma_short'].iloc[-2] < df['sma_long'].iloc[-2] and df['sma_short'].iloc[-1] > df['sma_long'].iloc[-1]:
        print("BUY SIGNAL")
        exchange.create_market_buy_order('BTCUSDT', 0.001)
    elif df['sma_short'].iloc[-2] > df['sma_long'].iloc[-2] and df['sma_short'].iloc[-1] < df['sma_long'].iloc[-1]:
        print("SELL SIGNAL")
        exchange.create_market_sell_order('BTCUSDT', 0.001)

# Chạy tự động mỗi 5 phút
while True:
    try:
        sma_crossover_strategy()
        time.sleep(300)  # 5 phút
    except Exception as e:
        print("Lỗi:", e)
        time.sleep(60)
