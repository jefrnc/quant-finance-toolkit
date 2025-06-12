# 📊 Guía Completa de Hotkeys para DAS Trader
> La colección más completa de hotkeys para optimizar tu trading en DAS Trader

## 📋 Tabla de Contenidos
- [Introducción](#introducción)
- [Configuración Inicial](#configuración-inicial)
- [Hotkeys Básicos](#hotkeys-básicos)
- [Gestión de Gráficos](#gestión-de-gráficos)
- [Entradas y Salidas Básicas](#entradas-y-salidas-básicas)
- [Position Sizing con Riesgo Fijo](#position-sizing-con-riesgo-fijo)
- [Gestión de Posiciones](#gestión-de-posiciones)
- [Stops y Targets Automáticos](#stops-y-targets-automáticos)
- [Hotkeys Avanzados](#hotkeys-avanzados)
- [Troubleshooting](#troubleshooting)
- [Mejores Prácticas](#mejores-prácticas)

## 🎯 Introducción

Esta guía recopila los mejores hotkeys de la comunidad de DAS Trader, organizados por nivel de complejidad. Cada script incluye explicación detallada y casos de uso.

### Requisitos Previos
- DAS Trader Pro versión 5.8.0.4 o superior (evitar 5.8.0.5)
- **Advanced Hotkeys** habilitado (crítico para scripts con cálculos)
- Cuenta con permisos de trading activos

## ⚙️ Configuración Inicial

### Habilitar Advanced Hotkeys
1. Ir a **Setup → Hot Key Configuration**
2. Marcar **"Advanced Hot Key"** en la esquina inferior izquierda
3. Click **OK** y reiniciar DAS Trader

### Configurar Double Click
1. Click derecho en gráfico → **Chart Area → Config Area**
2. Activar **"Double click to trade"**
3. Habilitar **"Show Orders"** y **"Show Order Line"**

### Configurar Montage
1. Click derecho en Montage → **Style → Stop Order**
2. Verificar que muestre correctamente Bid/Ask

## 🔤 Hotkeys Básicos

### Control de Precio y Shares
```
# Aumentar precio +0.01
KeyUp: Price=Price+0.01

# Disminuir precio -0.01
KeyDown: Price=Price-0.01

# Aumentar shares +100
KeyRight: Share=Share+100

# Disminuir shares -100
KeyLeft: Share=Share-100
```

### Cancelar Órdenes
```
# Cancelar todas las órdenes del símbolo actual
ESC: CXL ALLSYMB

# Cancelar TODAS las órdenes de la cuenta
Ctrl+Shift+Q: CXL ALL

# PÁNICO - Cerrar todas las posiciones (seguro, no cambia bias)
Shift+ESC: ROUTE=MARKET;Share=Pos;TIF=DAY+;SELLCOVER=Send;BUYCOVER=Send
```

## 📈 Gestión de Gráficos

### Cambio de Timeframes
```
# 1 minuto - 1 día
F1: MinuteChart 1 1d; ZoomFit

# 5 minutos - 1 día
F2: MinuteChart 5 1d; ZoomFit

# 1 hora - 15 días
F3: MinuteChart 60 15d; ZoomFit

# Diario - 1 año
F4: DayChart 1d 365d; ZoomFit

# Semanal - 4 años
F5: DayChart 1w 4y; ZoomFit

# Mensual - 5 años
F6: DayChart 1m 5y

# 12 horas
F7: MinuteChart 720 160; ZoomFit
```

### Herramientas de Análisis
```
# Línea horizontal
PageDown: HorizontalLine

# Línea Fibonacci
PageUp: FibonacciLine

# Ajustar zoom
Insert: ZoomFit
NumPad+: ZoomIn
NumPad-: ZoomOut

# Crear alerta
Alt+Shift+5: AlertName=newalert;AlertType=LastPrice;AlertOperator==;AddAlert
```

## 💰 Entradas y Salidas Básicas

### Compras con Cantidades Fijas

#### ARCA Route (Más común)
```
# Comprar 10 acciones al BID
Alt+Ctrl+A: ROUTE=ARCAL;Price=Bid;Share=10;TIF=DAY+;BUY=Send

# Comprar 25 acciones al BID
Alt+Ctrl+B: ROUTE=ARCAL;Price=Bid;Share=25;TIF=DAY+;BUY=Send

# Comprar 50 acciones al BID
Alt+Ctrl+7: ROUTE=ARCAL;Price=Bid;Share=50;TIF=DAY+;BUY=Send

# Comprar 100 acciones al BID
Alt+Ctrl+D: ROUTE=ARCAL;Price=Bid;Share=100;TIF=DAY+;BUY=Send
```

#### Con Slippage (ASK + cents)
```
# Comprar 25 al ASK +0.02
Alt+Ctrl+I: ROUTE=ARCAL;Price=Ask+0.02;Share=25;TIF=DAY+;BUY=Send

# Comprar 50 al ASK +0.02
Alt+Ctrl+J: ROUTE=ARCAL;Price=Ask+0.02;Share=50;TIF=DAY+;BUY=Send

# Comprar 100 al ASK +0.02
Alt+Ctrl+K: ROUTE=ARCAL;Price=Ask+0.02;Share=100;TIF=DAY+;BUY=Send
```

### Ventas con Cantidades Fijas
```
# Vender 10 acciones al ASK
Alt+Ctrl+O: ROUTE=ARCAL;Price=Ask;Share=10;TIF=DAY+;SELL=Send

# Vender 25 acciones al ASK
Alt+Ctrl+P: ROUTE=ARCAL;Price=Ask;Share=25;TIF=DAY+;SELL=Send

# Vender 50 acciones al ASK
Alt+Ctrl+Q: ROUTE=ARCAL;Price=Ask;Share=50;TIF=DAY+;SELL=Send

# Vender 100 acciones al ASK
Alt+Ctrl+R: ROUTE=ARCAL;Price=Ask;Share=100;TIF=DAY+;SELL=Send
```

## 📊 Position Sizing con Riesgo Fijo

### Sistema de Doble Click para Stop Loss

#### Paso 1: Capturar Stop (Shift+S)
```
$MYSTOP=Price;
LOG=Stop guardado en:$MYSTOP;
```

#### Paso 2: Entrada con Riesgo de $1 (Shift+B) - Versión Segura
```
CXL ALLSYMB;
Price=Ask+0.01;
Share=1/Abs(Price-$MYSTOP);
Share=Floor(Share);
Share=Max(1,Share);
Share=Min(1000,Share);
ROUTE=SMRTL;
TIF=DAY+;
BUY=Send;
TriggerOrder=RT:STOP STOPTYPE:MARKET PX:$MYSTOP ACT:SELLCOVER STOPPRICE:$MYSTOP QTY:Pos TIF:DAY+
```

### Hotkeys Todo-en-Uno con Riesgo Fijo

#### Long con $20 de riesgo y stop automático (Seguro)
```
CXL ALLSYMB;
$buyprice=Ask;
$risk=20;
$mystop=Price;
$pricetostop=$buyprice-$mystop;
$amount=$risk/$pricetostop;
Share=$amount;
ROUTE=SMRTL;
Price=$buyprice*1.005;
Price=ROUND2;
TIF=DAY+;
BUY=Send;
TriggerOrder=RT:STOP STOPTYPE:MARKET PX:$mystop ACT:SELLCOVER STOPPRICE:$mystop QTY:Pos TIF:DAY+
```

#### Short con $20 de riesgo y stop automático (Seguro)
```
CXL ALLSYMB;
$sellprice=Bid;
$risk=20;
$mystop=Price;
$pricetostop=$mystop-$sellprice;
$amount=$risk/$pricetostop;
Share=$amount;
ROUTE=SMRTL;
Price=$sellprice*0.995;
Price=ROUND2;
TIF=DAY+;
SELL=Send;
TriggerOrder=RT:STOP STOPTYPE:MARKET PX:$mystop ACT:BUYCOVER STOPPRICE:$mystop QTY:Pos TIF:DAY+
```

#### Riesgo como % del Buying Power (0.5%)
```
CXL ALLSYMB;
StopPrice=Price;
Share=BP*0.005/Abs(Ask-StopPrice);
Share=Floor(Share);
Share=Max(1,Share);
ROUTE=SMRTL;
Price=Ask+0.02;
TIF=DAY+;
BUY=Send;
```

### Tabla de Referencia Rápida
| Stop Loss | Acciones para $1 riesgo |
|-----------|------------------------|
| 1 cent    | 100 acciones          |
| 2 cents   | 50 acciones           |
| 5 cents   | 20 acciones           |
| 10 cents  | 10 acciones           |
| 20 cents  | 5 acciones            |

## 📉 Gestión de Posiciones

### ⚠️ IMPORTANTE: Uso de BUYCOVER y SELLCOVER
Para evitar cambiar accidentalmente de bias (de largo a corto o viceversa), usa estos comandos:

| Acción | Comando | Resultado |
|--------|---------|-----------|
| Abrir largo | BUY=Send | Compra acciones |
| Cerrar largo | SELLCOVER=Send | Solo vende lo que tienes (NO abre short) |
| Abrir corto | SELL=Send | Vende en corto |
| Cerrar corto | BUYCOVER=Send | Solo cubre lo que debes (NO abre largo) |

### Salidas Parciales Largas (Seguras)
```
# Vender 10% de la posición - NO abrirá short
Alt+Ctrl+E: ROUTE=ARCAL;Price=Ask;Share=Pos*0.1;TIF=DAY+;SELLCOVER=Send

# Vender 25% de la posición - NO abrirá short
Alt+Ctrl+F: ROUTE=ARCAL;Price=Ask;Share=Pos*0.25;TIF=DAY+;SELLCOVER=Send

# Vender 50% de la posición - NO abrirá short
Alt+Ctrl+G: ROUTE=ARCAL;Price=Ask;Share=Pos*0.5;TIF=DAY+;SELLCOVER=Send

# Vender 100% de la posición - NO abrirá short
Alt+Ctrl+H: ROUTE=ARCAL;Price=Ask;Share=Pos;TIF=DAY+;SELLCOVER=Send
```

### Salidas Parciales Cortas (Seguras)
```
# Cubrir 10% de la posición - NO abrirá largo
Alt+Ctrl+S: ROUTE=ARCAL;Price=Bid;Share=Pos*0.1;TIF=DAY+;BUYCOVER=Send

# Cubrir 25% de la posición - NO abrirá largo
Alt+Ctrl+T: ROUTE=ARCAL;Price=Bid;Share=Pos*0.25;TIF=DAY+;BUYCOVER=Send

# Cubrir 50% de la posición - NO abrirá largo
Alt+Ctrl+U: ROUTE=ARCAL;Price=Bid;Share=Pos*0.5;TIF=DAY+;BUYCOVER=Send

# Cubrir 100% de la posición - NO abrirá largo
Alt+Ctrl+V: ROUTE=ARCAL;Price=Bid;Share=Pos;TIF=DAY+;BUYCOVER=Send
```

## 🛡️ Stops y Targets Automáticos

### Stop Loss Básicos
```
# Stop para posición larga (5% abajo)
Alt+Ctrl+6: ROUTE=STOP;StopType=Market;StopPrice=Bid*0.95;Share=Pos;TIF=DAY;SELL=Send

# Stop para posición corta (5% arriba)
Alt+Ctrl+5: ROUTE=STOP;StopType=Market;StopPrice=Ask*1.05;Share=Pos;TIF=DAY;BUY=Send
```

### Stop Loss Dinámico Basado en ATR
```
# Stop con 2x ATR (Seguro - no abre short)
ROUTE=STOP;
StopType=Market;
Share=Pos;
StopPrice=AvgCost-ATR*2;
StopPrice=ROUND2;
TIF=DAY+;
SELLCOVER=Send
```

### Range Orders (OCO) - Stop y Target
```
# Long con stop y target 3R (Seguro)
CXL ALLSYMB;
$risk=20;
$mystop=Price;
$buyPrice=Ask;
$target=$buyPrice+($buyPrice-$mystop)*3;
Share=$risk/Abs($buyPrice-$mystop);
Share=Floor(Share);
ROUTE=SMRTL;
Price=$buyPrice+0.01;
TIF=DAY+;
BUY=Send;
TriggerOrder=RT:STOP STOPTYPE:RANGE 
LowPrice:$mystop 
HighPrice:$target 
ACT:SELLCOVER QTY:Pos TIF:DAY+
```

### Trailing Stop
```
# Trailing stop de 10 cents (Seguro)
ROUTE=SMRTL;
Price=Ask+0.10;
Share=100;
TIF=DAY+;
BUY=Send;
TriggerOrder=RT:STOP STOPTYPE:TRAILING STOPPRICE:0.10 ACT:SELLCOVER QTY:POS TIF:DAY+
```

## ⚠️ ADVERTENCIA CRÍTICA: Evitar Cambio Accidental de Bias

### El Problema
Si estás **SHORT 100 acciones** y ejecutas **BUY 200 acciones**:
1. Primero cubres las 100 cortas
2. ¡Luego abres 100 largas sin querer!

### La Solución: SIEMPRE usar BUYCOVER/SELLCOVER para cerrar
- **SELLCOVER**: Solo vende lo que tienes largo (nunca abre short)
- **BUYCOVER**: Solo cubre lo que tienes corto (nunca abre largo)

Esto es **CRÍTICO** para evitar pérdidas por cambiar de dirección accidentalmente.

## 🚀 Hotkeys Avanzados

### Scalping Rápido (Seguro)
```
# Entrada rápida con stop automático de 5 cents
CXL ALLSYMB;
Share=200;
Price=Ask;
ROUTE=SMRTL;
TIF=DAY+;
BUY=Send;
TriggerOrder=RT:STOP STOPTYPE:MARKET PX:AvgCost-0.05 ACT:SELLCOVER STOPPRICE:AvgCost-0.05 QTY:Pos TIF:DAY+
```

### Position Builder (Promediando)
```
# Agregar a posición existente con mismo riesgo
Share=Pos;
Price=Ask+0.01;
ROUTE=SMRTL;
TIF=DAY+;
BUY=Send
```

### Reversal Rápido
```
# De largo a corto
CXL ALLSYMB;
Share=Pos*2;
Price=Bid-0.01;
ROUTE=SMRTL;
TIF=DAY+;
SELL=Send
```

### Scripts para Penny Stocks (<$1)
```
# Entrada con límites ajustados
CXL ALLSYMB;
StopPrice=Price;
Price=Ask+0.001;
Risk=1;
Dist=Abs(Price-StopPrice);
Dist=Max(0.005,Dist);
Share=Risk/Dist;
Share=Floor(Share);
Share=Max(1,Share);
Share=Min(200,Share);
ROUTE=ARCA;
TIF=DAY+;
BUY=Send
```

## 🔧 Troubleshooting

### Error "Invalid Shares"
1. Verificar que **Advanced Hotkeys** esté habilitado
2. No usar versión 5.8.0.5 (tiene bug conocido)
3. Verificar que el montage esté en el símbolo correcto
4. En modo Replay, muchos hotkeys no funcionan

### Cálculos no funcionan
Test simple:
```
Share=10+10;
LOG=Resultado:Share;
```
Si muestra 20, los cálculos funcionan. Si muestra 10, Advanced Hotkeys no está habilitado.

### TriggerOrder no se ejecuta
- Solo funciona en horario regular (9:30 AM - 4:00 PM EST)
- No funciona en Replay Mode
- La orden principal debe ejecutarse completamente

## 💡 Mejores Prácticas

### Organización de Teclas
- **F1-F7**: Timeframes
- **Alt+Ctrl+Letras**: Órdenes de trading
- **Shift+Tecla**: Funciones especiales
- **ESC/Ctrl**: Cancelaciones y emergencias

### Seguridad
1. Siempre probar nuevos hotkeys en cuenta DEMO
2. Comenzar con cantidades pequeñas
3. Tener siempre un hotkey de PÁNICO configurado
4. Verificar el símbolo antes de ejecutar

### Optimización
1. Agrupar hotkeys relacionados en teclas cercanas
2. Usar la mano izquierda para modificadores (Shift, Ctrl, Alt)
3. Mantener los más importantes en teclas de fácil acceso
4. Documentar tus hotkeys personalizados

### Para Diferentes Estilos de Trading

#### Day Trading
- Énfasis en entradas/salidas rápidas
- Stops ajustados
- Salidas parciales

#### Scalping
- Hotkeys de ejecución instantánea
- Sin confirmaciones
- Tamaños fijos

#### Swing Trading
- Cálculos basados en ATR
- Stops más amplios
- Targets múltiples

## 📝 Notas Finales

Esta guía es un documento vivo que se actualiza con las contribuciones de la comunidad. Los scripts han sido probados pero siempre verifica en tu entorno específico.

### Recursos Adicionales
- [Documentación oficial DAS Trader](https://dastrader.com)
- [Foros Bear Bull Traders](https://forums.bearbulltraders.com)
- [DAS Trader Scripts en GitHub](https://github.com/topics/das-trader)

### Contribuciones
Si tienes hotkeys útiles que no están en esta guía, compártelos con la comunidad. Entre todos podemos hacer el trading más eficiente.

---
*Última actualización: Diciembre 2024*
*Versión: 1.1 - Añadida seguridad con BUYCOVER/SELLCOVER*
*Compilado por la comunidad de traders de DAS*