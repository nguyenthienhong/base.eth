# base.eth # Tín hiệu
if df['sma_short'].iloc[-2] < df['sma_long'].iloc[-2] and df['sma_short'].iloc[-1] > df['sma_long'].iloc[-1]:
    print("BUY SIGNAL")
    exchange.create_market_buy_order('BTCUSDT', 0.001)
elif df['sma_short'].iloc[-2] > df['sma_long'].iloc[-2] and df['sma_short'].iloc[-1] < df['sma_long'].iloc[-1]:
    print("SELL SIGNAL")
    exchange.create_market_sell_order('BTCUSDT', 0.001)
