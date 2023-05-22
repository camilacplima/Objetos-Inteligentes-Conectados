# Bibliotecas e Importações
from machine import Pin
from umqtt.simple import MQTTClient
import ujson
import network
import utime as time
import dht

# Setup do botão
BUTTON_PIN = Pin(14, Pin.IN, Pin.PULL_UP)

# Setup do device
DEVICE_ID = "wokwi001"

# Setup do WiFi
WIFI_SSID       = "Wokwi-GUEST"
WIFI_PASSWORD   = ""

# Setup do MQTT
MQTT_BROKER             = "test.mosquitto.org"
MQTT_CLIENT             = DEVICE_ID
MQTT_TELEMETRY_TOPIC    = "iot/telemetry"
MQTT_CONTROL_TOPIC      = "iot/control"

# Setup do Sensor DHT 
DHT_PIN = Pin(15)

# Setup das lâmpadas LED
RED_LED     = Pin(12, Pin.OUT)
BLUE_LED    = Pin(13, Pin.OUT)
FLASH_LED   = Pin(2, Pin.OUT)

# Setup de iterações máximas
MAX_ITERATIONS = 50
iteration = 0

# Estado do botão
button_prev_state = BUTTON_PIN.value()


def did_recieve_callback(topic, message):
    print('\n\nData Recieved! \ntopic = {0}, message = {1}'.format(topic, message))

    # device_id/lamp/color/state
    # device_id/lamp/state
    # lamp/state
    if topic == MQTT_CONTROL_TOPIC.encode():
        if message == ('{0}/lamp/red/on'.format(DEVICE_ID)).encode():
            RED_LED.on()
        elif message == ('{0}/lamp/red/off'.format(DEVICE_ID)).encode():
            RED_LED.off()
        elif message == ('{0}/lamp/blue/on'.format(DEVICE_ID)).encode():
            BLUE_LED.on()
        elif message == ('{0}/lamp/blue/off'.format(DEVICE_ID)).encode():
            BLUE_LED.off()
        elif message == ('{0}/lamp/on'.format(DEVICE_ID)).encode() or message == b'lamp/on':
            RED_LED.on()
            BLUE_LED.on()
        elif message == ('{0}/lamp/off'.format(DEVICE_ID)).encode() or message == b'lamp/off':
            RED_LED.off()
            BLUE_LED.off()
        elif message == ('{0}/status'.format(DEVICE_ID)).encode() or message == ('status').encode():
            global telemetry_data_old
            mqtt_client_publish(MQTT_TELEMETRY_TOPIC, telemetry_data_old)
        else:
            return
        
        send_led_status()

def mqtt_connect():
    print("Connecting to MQTT broker ...", end="")
    mqtt_client = MQTTClient(MQTT_CLIENT, MQTT_BROKER, user="", password="")
    mqtt_client.set_callback(did_recieve_callback)
    mqtt_client.connect()
    print("Connected.")
    mqtt_client.subscribe(MQTT_CONTROL_TOPIC)
    return mqtt_client

def create_json_data(temperature, humidity):
    data = ujson.dumps({
        "device_id": DEVICE_ID,
        "temp": temperature,
        "humidity": humidity,
        "type": "sensor"
    })
    return data

def mqtt_client_publish(topic, data):
    print("\nUpdating MQTT Broker...")
    mqtt_client.publish(topic, data)
    print(data)

def send_led_status():
    data = ujson.dumps({
        "device_id": DEVICE_ID,
        "red_led": "ON" if RED_LED.value() == 1 else "OFF",
        "blue_led": "ON" if BLUE_LED.value() == 1 else "OFF",
        "type": "lamp"
    })
    mqtt_client_publish(MQTT_TELEMETRY_TOPIC, data)


# Conectando ao WiFi
wifi_client = network.WLAN(network.STA_IF)
wifi_client.active(True)
print("Connecting device to WiFi")
wifi_client.connect(WIFI_SSID, WIFI_PASSWORD)

# Esperando até o Wifi ser conectado
while not wifi_client.isconnected():

    time.sleep(0.1)
print("WiFi Connected!")
print(wifi_client.ifconfig())

# Conectando ao MQTT
mqtt_client = mqtt_connect()
RED_LED.off()
BLUE_LED.off()
mqtt_client_publish(MQTT_CONTROL_TOPIC, 'lamp/off')
dht_sensor = dht.DHT22(DHT_PIN)
telemetry_data_old = ""

while iteration < MAX_ITERATIONS:
    mqtt_client.check_msg()
    print(". ", end="")

    FLASH_LED.on()
    try:
      dht_sensor.measure()
    except:
      pass
    
    time.sleep(0.2)
    FLASH_LED.off()

    telemetry_data_new = create_json_data(dht_sensor.temperature(), dht_sensor.humidity())

    print("Temperature: ", dht_sensor.temperature())
    print("Humidity: ", dht_sensor.humidity())
    print("LED Status - Red: ", RED_LED.value())
    print("LED Status - Blue: ", BLUE_LED.value())

    if telemetry_data_new != telemetry_data_old:
        mqtt_client_publish(MQTT_TELEMETRY_TOPIC, telemetry_data_new)
        telemetry_data_old = telemetry_data_new

    # Checando botão
    button_current_state = BUTTON_PIN.value()
    if button_prev_state and not button_current_state:
        if RED_LED.value() == 1 and BLUE_LED.value() == 1:
            RED_LED.off()
            BLUE_LED.off()
            mqtt_client_publish(MQTT_CONTROL_TOPIC, 'lamp/off')
        else:
            RED_LED.on()
            BLUE_LED.on()
            mqtt_client_publish(MQTT_CONTROL_TOPIC, 'lamp/on')
    button_prev_state = button_current_state

    time.sleep(0.1)

    # Incrementa o contador de iteração
    iteration += 1
