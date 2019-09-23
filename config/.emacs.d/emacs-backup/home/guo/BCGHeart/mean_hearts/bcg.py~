
from __future__ import absolute_import
from __future__ import division
from __future__ import print_function

import numpy as np

from absl import app
from collections import Counter
from scipy.signal import lfilter


def _parse_integer(bytes):
    int_str = bytes.decode('ASCII')
    integers = []
    try:
        integers = list(map(int, int_str.split(',')))
    except e:
        print(int_str)
        raise e
    return integers

def parse_bcg(bytes):
    data = []
    packet = []
    start_index = 0
    end_index = 0
    for ind, byte in enumerate(bytes):
        if byte == ord('#'):
            if end_index - start_index > 0:
                integers = _parse_integer(bytes[start_index:end_index])
                data.extend(integers)
            start_index = ind + 1
            end_index = ind + 1
        if byte == ord('\r'):
            end_index = ind
    if end_index - start_index > 0:
        integers = _parse_integer(bytes[start_index:end_index])
        data.extend(integers)
    return data


def load_bcg(filepath, offset=0, filter=True, notch=True):
    f = open(filepath, 'rb')
    raw = f.read()
    f.close()
    signal = parse_bcg(raw)
    signal = signal[offset:]
    signal = list(map(lambda x: -1*x, signal))
    if filter:
        signal = bandpass_filter(signal)
    if notch:
        signal = notch_filter(signal)
    return signal


def bandpass_filter(signal):
    b = np.loadtxt("filter/bcg_bandpass_b.csv", delimiter=',')
    a = np.loadtxt("filter/bcg_bandpass_a.csv", delimiter=',')
    signal_filter = lfilter(b, a, signal)
    return signal_filter

def notch_filter(signal):
    b = np.loadtxt("filter/ecg_notch_b.csv", delimiter=',')
    a = np.loadtxt("filter/ecg_notch_a.csv", delimiter=',')
    signal_filter = lfilter(b, a, signal)
    return signal_filter
