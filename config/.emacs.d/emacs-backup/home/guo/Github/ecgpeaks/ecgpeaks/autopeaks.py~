""" find peaks in a periodic signal such as ECG
    Assume:
      1. cycle is stable and have one strong period signal
      2. target peaks have enough features
    Proprosal:
      1. peak value features
      2. cycle priors
      3. cycle transition priors
      4. autodetect to detech peaks or valleys
    Implemention:
      1. merge findpeaks results
      2. analysis peak results
      3. adjust findpeaks function parameters
"""

from __future__ import absolute_import
from __future__ import division
from __future__ import print_function

import numpy as np
from collections import deque
import peakutils

from absl import app

import pdb


class AutoPeaks(object):
    """ online findpeaks for periodic signals """
    def __init__(self, thres=0.65, min_dist=80, fs=200, buffer_size=None):
        if buffer_size is None:
            buffer_size = int(fs * 3)
        self._signal_buffer = deque(maxlen=buffer_size)
        self._peak_value_buffer = deque()
        self._peak_index_buffer = deque()
        self._signal_index = -1 # starts from 0
        self._update_counter = 0
        self.thres = thres
        self.min_dist = min_dist

    @property
    def peak_values(self):
        """ get the peak values """
        values = list(self._peak_value_buffer)
        self._peak_value_buffer.clear()
        return values
        #while self._peak_value_buffer:
        #    index = self._peak_value_buffer.popleft()
        #    yield index

    @property
    def peak_indexes(self):
        """ get the peak indexes """
        indexes = list(self._peak_index_buffer)
        self._peak_index_buffer.clear()
        return indexes
        #while self._peak_index_buffer:
        #    index = self._peak_index_buffer.popleft()
        #    yield index

    def clear(self):
        """ clear signal buffer and reset signal index"""
        self._signal_buffer.clear()
        self._signal_index = -1
        
    def findpeaks(self, value):
        """ find peaks in signal buffer,
            add peak indexes and peak values to buffer
        """
        self._signal_index += 1
        self._update_counter += 1
        signal = self._signal_buffer
        signal.append(value)
        if len(signal) != signal.maxlen:
            return
        update_thres = signal.maxlen / 2
        if self._update_counter < update_thres:
            return
        self._update_counter = 0

        ipks = peakutils.indexes(
            np.asarray(signal), thres=self.thres, min_dist=self.min_dist)
        ipk_offset = self._signal_index - signal.maxlen + 1
        chosen_ipks_start = int( signal.maxlen / 4 )
        chosen_ipks_end = signal.maxlen - int( signal.maxlen / 4 )
        for ipk in ipks:
            if ipk_offset == 0 and ipk < chosen_ipks_end:
                self._peak_index_buffer.append(ipk)
                self._peak_value_buffer.append(signal[ipk])
                continue
            if ipk < chosen_ipks_start or ipk >= chosen_ipks_end:
                continue
            self._peak_index_buffer.append(ipk + ipk_offset)
            self._peak_value_buffer.append(signal[ipk])


def main(argv):
    del argv
    from scipy.io import loadmat
    filepath = "/home/guo/data/sleepstage/database/ISRUC_Sleep/" + \
        "ExtractedChannels/subgroupI-Extractedchannels/subject1.mat"
    data = loadmat(filepath)
    ecg = data["X2"].flatten()[:5000]
    autopeaks = AutoPeaks(thres=0.65, min_dist=80, fs=200)
    list(map(autopeaks.findpeaks, ecg))
    ipks = autopeaks.peak_indexes
    import matplotlib.pyplot as plt
    fig = plt.figure()
    ax = fig.add_subplot(111)
    ax.plot(ecg)
    ax.plot(ipks, ecg[ipks], 'o')
    plt.show()
    
if __name__ == "__main__":
    app.run(main)
