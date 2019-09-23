
import numpy as np
import requests

from absl import app
from autopeaks2 import FindPeaksMaster

obj = FindPeaksMaster(fs=500)

def main(unused_args):
    del unused_args
    signal = np.loadtxt("yjj.txt")
    print("signal dur: {}".format(len(signal) / 500 / 60))
    server = True
    if server:
        signal = ",".join(map(str, signal.tolist()))
        resp = requests.post("http://localhost:8002", json={'signal':signal})
        print(resp.json())
    else:
        signal = signal[:500*30]
        list(map(obj.findpeaks, signal))
        print(obj.peak_intervals)
    
if __name__ == '__main__':
    app.run(main)
