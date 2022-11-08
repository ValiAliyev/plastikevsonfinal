# plastikevsonfinal
import telebot
import pyfirmata
from pyfirmata import util
import time

Controler = pyfirmata.ArduinoMega('COM5')
PassCode = 'hello'
Token = '5633464928:AAExHq_DNfHIKRog3wQT5VQFg9NLZxxmjoc'
Home = telebot.TeleBot(Token)
MyGuide = open('F:\MyHome\MyGuide.png','rb')

#Pinlerin Teyini
DoorOpenPin = Controler.digital[9]
DoorClosePin = Controler.digital[10]
InLightsPin = Controler.digital[13]
OutLightsPin = Controler.digital[12]
GasSensPin = Controler.analog[0]
DoorCheckPin = Controler.digital[11]


@Home.message_handler(commands=['Menu'])
def Menu(message):
    Home.reply_to(message,"Əmrlər menüsu: \n /Menu \n /Info \n /Guide \n /InLightsOn \n /InLightsOff \n /OutLightsOn \n /OutLightsOff \n /OpenDoor \n /CloseDoor \n /GasRead \n /DoorCheck")



@Home.message_handler(commands=['DoorCheck'])
def DoorCheck(message):
    print('Qapı yoxlaması...')
    reader = pyfirmata.util.Iterator(Controler)
    reader.start()
    sensor = DoorCheckPin
    sensor.mode = pyfirmata.INPUT
    sensor.enable_reporting()
    while True:
        data = str(sensor.read())
        if data != 'None':
            print(sensor.read())
            if sensor.read() == True:
                Home.reply_to(message,"Qapı bağlıdır!")
            elif sensor.read() == False:
                Home.reply_to(message, "Qapı açıqdır!")
            print('Melumat gonderildi!')
            break



@Home.message_handler(commands=['OpenDoor'])
def OpenDoor(message):
    DoorOpenPin.write(1)
    time.sleep(3)
    DoorOpenPin.write(0)
    print('Qapı açıldı!')
    Home.reply_to(message, "Qapı açıldı!")

@Home.message_handler(commands=['CloseDoor'])
def CloseDoor(message):
    DoorClosePin.write(1)
    time.sleep(2)
    DoorClosePin.write(0)
    print('Qapı Bağlandı')
    Home.reply_to(message, "Qapı bağlandı!")


@Home.message_handler(commands=['Info'])
def Info(message):
    Home.reply_to(message, 'Layihə haqqında ətraflı məlumat:\nhttps://cutt.ly/tNgo1gL')


@Home.message_handler(commands=['Guide'])
def Guide(message):
    User = int(message.chat.id)
    Home.reply_to(message, 'Sizə istifadəçi təlimatını göndəririk...')
    Home.send_photo(message.chat.id,MyGuide)



@Home.message_handler(commands=['InLightsOn'])
def LightsOn(message):
    InLightsPin.write(1)
    print('Isiq yandi!')
    Home.reply_to(message,'Isiq yandi!')

@Home.message_handler(commands=['InLightsOff'])
def LightsOff(message):
    InLightsPin.write(0)
    print('Isiqlar sondu!')
    Home.reply_to(message,'Isiq sondu!')



@Home.message_handler(commands=['OutLightsOn'])
def LightsOn(message):
    OutLightsPin.write(1)
    print('Isiq yandi!')
    Home.reply_to(message,'Isiq yandi!')

@Home.message_handler(commands=['OutLightsOff'])
def LightsOff(message):
    OutLightsPin.write(0)
    print('Isiqlar sondu!')
    Home.reply_to(message,'Isiq sondu!')



@Home.message_handler(commands=['GasRead'])
def GasRead(message):
    print('Qaz sensoru oxundu!')
    reader = pyfirmata.util.Iterator(Controler)
    reader.start()
    sensor = GasSensPin
    sensor.enable_reporting()
    while True:
        data = str(sensor.read())
        if data != 'None':
            print(sensor.read())
            Home.reply_to(message,"Havadaki qaz miqdarı: {}".format(data))
            print('Melumat gonderildi!')
            break

@Home.message_handler(commands=['clear'])
def StartCommand(message):
    Hosts = open('F:\MyHome\Hosts.txt', 'w')

@Home.message_handler(commands=['start'])
def StartCommand(message):
    Home.reply_to(message, 'Ev sahibləri üçün giriş:\n /MyHome\nYeni gələnlər üçün giriş:\n /Register')


@Home.message_handler(commands=['Register'])
def StartCommand(messaj):
    Home.reply_to(messaj, 'Evinizə xoş gəldiniz! Parolu daxil edin.')

    @Home.message_handler(func=lambda messaj: True)
    def IDCheck(messaj):
        UserInput = messaj.text
        UserID = messaj.chat.id
        print(UserInput)
        print(UserID)


        if str(UserInput).lower() == str(PassCode):
            Hosts = open('F:\MyHome\Hosts.txt', 'a')
            Hosts.writelines(str(UserID))
            Hosts.writelines(' ')

            HostList = []
            Hosts = open('F:\MyHome\Hosts.txt', 'r')
            HostList.append(Hosts.read())
            print(HostList)
            for i in HostList:
                HostList2 = HostList[0].split(' ')
                print(HostList2)
                if str(UserID) in HostList2:
                    print('Yeni sahibin qeydiyyati tamamlandi!')
                    Home.reply_to(messaj,
                                  "Ev sahibinin tanınması başa çatdı! Əmrlər menüsu: \n /Menu \n /Info \n /Guide \n /InLightsOn \n /InLightsOff \n /OutLightsOn \n /OutLightsOff \n /OpenDoor \n /CloseDoor \n /GasRead \n /DoorCheck")
                else:
                    print('New user!')
                    Home.reply_to(messaj, 'You are a new user!')
        elif str(UserInput) != str(PassCode):
            print("Uğursuz giriş cəhdi!")
            Home.reply_to(messaj, 'Şifrənizi daxil edin.')



@Home.message_handler(commands=['MyHome'])
def StartCommand(message):
    Home.reply_to(message, 'Evinizə xoş gəldiniz! Adinizi daxil edin.')

    @Home.message_handler(func=lambda m: True)
    def IDCheck(message):
        UserInput = message.text
        UserID = message.chat.id
        print(UserInput)
        print(UserID)

        HostList = []
        Hosts = open('F:\MyHome\Hosts.txt', 'r')
        HostList.append(Hosts.read())
        print(HostList)
        for i in HostList:
            HostList2 = HostList[0].split(' ')
            print(HostList2)
            if str(UserID) in HostList2:
                print('Yeni sahibin taninmasi basa catdi!')
                Home.reply_to(message,
                              "Ev sahibinin tanınması başa çatdı! Əmrlər menüsu: \n /Menu \n /Info \n /Guide \n /InLightsOn \n /InLightsOff \n /OutLightsOn \n /OutLightsOff \n /OpenDoor \n /CloseDoor \n /GasRead \n /DoorCheck")
            elif str(UserID) not in HostList2:
                Home.reply_to(message,
                              'Siz ev sahibi deyilsiniz! \nQeydiyyatdan keçmək üçün /Register əmrindən istifadə edin.')


while True:
    Home.polling()
