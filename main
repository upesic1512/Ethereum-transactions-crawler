import json
import sys
from requests import get
from matplotlib import pyplot as plt
from datetime import date,time,datetime


API_KEY = "MHH21PBS9BYFDTFBBS6CR55QY8WTCKI93A"
BASE_URL = 'https://api.etherscan.io/api'
ETHER_VALUE = 10 ** 18



print("Enter your wallet address: ")
address = input()
print("Enter block number: ")
blok = input()


def make_api_url(module, action,address, **kwargs):
    url = BASE_URL + f"?module={module}&action={action}&address={address}&apikey={API_KEY}"

    for key, value in kwargs.items():
        url += f"&{key}={value}"
    return url




def get_transactions(addres,blok):
    transactions_url = make_api_url("account","txlist", addres, startblock = blok, endblock=99999999, page=1, offset = 10000,sort = "asc" )
    responce = get(transactions_url)
    data = responce.json()["result"]

    internal_transactions_url = make_api_url("account","txlistinternal", addres, startblock = blok, endblock=99999999, page=1, offset = 10000,sort = "asc" )
    responce2 = get(internal_transactions_url)
    data2 = responce2.json()["result"]

    data.extend(data2)
    data.sort(key = lambda x: int(x["timeStamp"]))

    for tx in data:
        to = tx["to"]
        from_address = tx["from"]
        value = int(tx["value"])/ETHER_VALUE
        time = datetime.fromtimestamp(int(tx["timeStamp"]))

        print("-------------------------------------")
        print("From: ",from_address)
        print("To: ", to)
        print("Value: ",value,"ETH")
        print("Time: ",time)

get_transactions(address,blok)

print("Enter date to check balance on that day")

my_date = input()
my_date = datetime.strptime(my_date,"%Y-%m-%d").date()
my_time = time(0, 0)
combined = datetime.combine(my_date,my_time)
if(combined > datetime.now()):
    print("Cannot check future dates")
    sys.exit(1)



def get_balance_at_time(address,blok,combined):
    transactions_url = make_api_url("account","txlist", address, startblock = blok, endblock=99999999, page=1, offset = 10000,sort = "asc" )
    responce = get(transactions_url)
    data = responce.json()["result"]

    internal_transactions_url = make_api_url("account","txlistinternal", address, startblock = blok, endblock=99999999, page=1, offset = 10000,sort = "asc" )
    responce2 = get(internal_transactions_url)
    data2 = responce2.json()["result"]

    data.extend(data2)
    data.sort(key = lambda x: int(x['timeStamp']))

    current_balance = 0
    balances = []
    times =[]
    for tx in data:
        to = tx["to"]
        from_address = tx["from"]
        value = int(tx["value"]) / ETHER_VALUE
        if "gasPrice" in tx:
            gas = int(tx["gasUsed"]) * int(tx["gasPrice"]) / ETHER_VALUE
        else:
            gas = int(tx["gasUsed"]) / ETHER_VALUE
        time = datetime.fromtimestamp(int(tx["timeStamp"]))

        money_in = to.lower() == address.lower()

        if money_in:
            current_balance += value
        else:
            current_balance -= value + gas

        balances.append(current_balance)
        times.append(time)

    plt.plot(times, balances)
    plt.show()

    for i in range(len(times)):
        if combined <= times[i]:
            k = i
            break
    return balances[k]


print("Exact value of ETH that was available on",combined,"was:",get_balance_at_time(address,blok,combined))

