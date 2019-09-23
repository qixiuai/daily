
from __future__ import absolute_import
from __future__ import division
from __future__ import print_function

import numpy as np

from edfplus import Edfplus
from bcg import load_bcg
from scipy.signal import lfilter


def load_ecg(filepath, filter=True):
    f = open(filepath, "rb")
    raw = f.read()
    f.close()
    edf = Edfplus(raw)
    ecg = edf.signals['ECG LL-RA']
    if filter:
        ecg = notch_filter(ecg)
    return ecg


def notch_filter(signal):
    b = np.loadtxt("filter/ecg_notch_b.csv", delimiter=',')
    a = np.loadtxt("filter/ecg_notch_a.csv", delimiter=',')
    signal_filter = lfilter(b, a, signal)
    return signal_filter    


if __name__ == '__main__':
    pass


