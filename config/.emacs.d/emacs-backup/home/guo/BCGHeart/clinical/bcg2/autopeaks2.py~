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

import itertools
import numpy as np
import findpeaks2
from collections import deque
from collections import namedtuple
from absl import app

import pdb


class FindPeaksMaster(object):

    def __init__(self, fs=500):
        self._workers = []
        threses = [0.1, 0.2]
        min_dists = [int(0.5*fs), int(0.6*fs), int(0.75*fs), int(0.85*fs)]
        directions = ["up", "down"]
        buffer_sizes = [4*fs]
        combinations = itertools.product(threses, min_dists, directions, buffer_sizes)
        for thres, min_dist, direction, buffer_size in combinations:
            findpeaks = FindPeaks(fs=fs,
                                  thres=thres,
                                  min_dist=min_dist,
                                  direction=direction,
                                  buffer_size=buffer_size)
            worker = FindPeaksWorker(findpeaks, nsamples=6)
            self._workers.append(worker)
        self._peak_index_buffer = deque()
        self._peak_interval_buffer = deque()
        self._peak_value_buffer = deque()
        self._counter = 0
        self._counter_maximum = 1*fs

    def findpeaks(self, value):
        self._counter += 1
        for worker in self._workers:
            worker.findpeaks(value)
        if self._counter < self._counter_maximum:
            return
        self._counter = 0
        losses = []
        for worker in self._workers:
            losses.append(worker.loss)
        best_worker_idx = np.argmin(losses)
        best_worker = self._workers[best_worker_idx]
        self._peak_index_buffer.extend(best_worker.peak_indices)
        self._peak_interval_buffer.extend(best_worker.peak_intervals)
        self._peak_value_buffer.extend(best_worker.peak_values)
        for worker in self._workers:
            worker.peak_indices
            worker.peak_values
            worker.peak_intervals

    @property
    def peak_indices(self):
        peak_indices = list(self._peak_index_buffer)
        self._peak_index_buffer.clear()
        return peak_indices

    @property
    def peak_intervals(self):
        intervals = list(self._peak_interval_buffer)
        self._peak_interval_buffer.clear()
        return intervals
    
    @property
    def peak_values(self):
        peak_values = list(self._peak_value_buffer)
        self._peak_value_buffer.clear()
        return peak_values


class FindPeaksWorker(object):

    def __init__(self, instance, nsamples=60):
        """ master fetch indices must be more than 1 miniutes one time"""
        self._instance = instance
        self._peak_indices_for_loss = deque(maxlen=nsamples)
        self._peak_index_buffer = deque()
        self._peak_interval_buffer = deque()
        self._peak_value_buffer = deque()
        self._last_peak_index = None
        
    def findpeaks(self, value):
        self._instance.findpeaks(value)

    @property
    def mean_and_std_intervals(self):
        intervals = np.diff(self._peak_indices_for_loss)
        fs = self._instance.fs
        hist,bin_edges = np.histogram(intervals, bins=5, range=(0.5*fs, 1.5*fs))
        ind = np.argmax(hist)
        bin_st, bin_ed = bin_edges[ind], bin_edges[ind+1]
        cluster = []
        for interval in intervals:
            if interval >= bin_st and interval <= bin_ed:
                cluster.append(interval)
        mean = np.median(cluster)
        #std = np.std(np.abs(np.diff(cluster)))
        std = np.std(cluster)
        return mean, std

    @property
    def loss(self):
        '''
        set loss to be error rate
        '''
        loss = 1000000000
        peak_indices = self._instance.peak_indices
        self._peak_indices_for_loss.extend(peak_indices)

        if self._last_peak_index is None:
            if len(peak_indices) > 1:
                diff = peak_indices[1] - peak_indices[0]
                self._last_peak_index = peak_indices[0] - diff
            elif len(peak_indices) == 1:
                self._last_peak_index = peak_indices[0] - self._instance.fs
            else:
                return loss

        if self._peak_index_buffer:
            last_peak_index = self._peak_index_buffer[-1]
        else:
            last_peak_index = self._last_peak_index

        self._peak_interval_buffer.extend(np.diff([last_peak_index] + peak_indices))
        self._peak_index_buffer.extend(peak_indices)

        peak_values = self._instance.peak_values
        self._peak_value_buffer.extend(peak_values)

        if len(self._peak_indices_for_loss) >= 2:
            #mean, std = self.mean_and_std_intervals
            intervals = np.diff(self._peak_indices_for_loss)
            #diff_abs = np.abs(np.diff(intervals))
            #mean = np.median(diff_abs)
            #diff = diff_abs - mean
            #loss = np.sum(diff * diff) / len(diff)
            #inerval_diffs_abs = np.abs(np.diff(intervals))
            #count = 0
            #for interval in intervals:
            #    if np.abs(interval - mean) >= 5*std:
            #        count += 1
            #loss = std + 100*count
            loss = np.std(intervals)
        return loss

    @property
    def peak_indices(self):
        peak_indices = list(self._peak_index_buffer)
        self._peak_index_buffer.clear()
        if len(peak_indices) > 0:
            self._last_peak_index = peak_indices[-1]
        return peak_indices

    @property
    def peak_intervals(self):
        intervals = list(self._peak_interval_buffer)
        self._peak_interval_buffer.clear()
        return intervals

    @property
    def peak_values(self):
        peak_values = list(self._peak_value_buffer)
        self._peak_value_buffer.clear()
        return peak_values


class FindPeaks(object):
    """ online findpeaks for periodic signals """
    def __init__(self, fs=500, thres=0.65, min_dist=300, direction="up", buffer_size=None):
        if buffer_size is None:
            buffer_size = int(fs * 2)
        self.fs = fs
        self._signal_buffer = deque(maxlen=buffer_size)
        self._peak_value_buffer = deque()
        self._peak_index_buffer = deque()
        self._peaks_buffer = deque()
        self._raw_extremas_buffer = deque()
        self._raw_peaks_buffer = deque()
        self._thres_abs_buffer = deque()
        self._signal_index = -1 # starts from 0
        self._update_counter = 0
        self.thres = thres
        self.min_dist = min_dist
        self.direction = direction

    @property
    def raw_extremas(self):
        extremas = list(self._raw_extremas_buffer)
        self._raw_extremas_buffer.clear()
        return extremas

    @property
    def raw_peaks(self):
        peaks = list(self._raw_peaks_buffer)
        self._raw_peaks_buffer.clear()
        return peaks

    @property
    def peaks(self):
        peaks = list(self._peaks_buffer)
        self._peaks_buffer.clear()
        return peaks

    @property
    def thres_abs(self):
        thres = list(self._thres_abs_buffer)
        self._thres_abs_buffer.clear()
        return thres

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
    def peak_indices(self):
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

        peaks, raw_extremas, raw_peaks, thres_abs = \
            findpeaks2.findpeaks2(np.asarray(signal),
                                  thres=self.thres,
                                  min_dist=self.min_dist,
                                  direction=self.direction)
        index_offset = self._signal_index - signal.maxlen + 1
        chosen_index_start = int(signal.maxlen / 4)
        chosen_index_end = signal.maxlen - int(signal.maxlen / 4)
        chosen_dist = chosen_index_end - chosen_index_start

        def filter_peaks(peaks):
            ret = []
            for peak in peaks:
                peak_index = peak.peak_index
                if index_offset == 0 and peak_index < chosen_index_end:
                    ret.append(peak)
                    continue
                if peak_index < chosen_index_start or peak_index >= chosen_index_end:
                    continue
                peak_index = peak_index + index_offset
                args = list(peak)
                args[0] = peak_index
                new_peak = findpeaks2.Peak(*args)
                ret.append(new_peak)
            return ret

        def filter_extremas(peaks):
            ret = []
            for peak in peaks:
                peak_index = peak.index
                if index_offset == 0 and peak_index < chosen_index_end:
                    ret.append(peak)
                    continue
                if peak_index < chosen_index_start or peak_index >= chosen_index_end:
                    continue
                peak_index = peak_index + index_offset
                args = list(peak)
                args[0] = peak_index
                new_peak = findpeaks2.Extrema(*args)
                ret.append(new_peak)
            return ret

        # add raw peaks to buffer
        chosen_raw_extremas = filter_extremas(raw_extremas)
        self._raw_extremas_buffer.extend(chosen_raw_extremas)

        # add raw peaks to buffer
        chosen_raw_peaks = filter_peaks(raw_peaks)
        self._raw_peaks_buffer.extend(chosen_raw_peaks)

        # add peaks to buffer
        chosen_peaks = filter_peaks(peaks)
        self._peaks_buffer.extend(chosen_peaks)
        
        # add thres_abs to buffer
        self._thres_abs_buffer.extend([thres_abs for _ in range(chosen_dist)])
        
        # add peak.peak_index, peak.peak_value to buffer
        self._peak_index_buffer.extend(list(map(lambda peak:peak.peak_index, chosen_peaks)))
        self._peak_value_buffer.extend(list(map(lambda peak:peak.peak_value, chosen_peaks)))
        
        """
        # add peak_index from peaks to peak_index_buffer
        ipks = list(map(lambda peak:peak.peak_index, peaks))
        for ipk in ipks:
            if ipk_offset == 0 and ipk < chosen_ipks_end:
                self._peak_index_buffer.append(ipk)
                self._peak_value_buffer.append(signal[ipk])
                continue
            if ipk < chosen_ipks_start or ipk >= chosen_ipks_end:
                continue
            self._peak_index_buffer.append(ipk + ipk_offset)
            self._peak_value_buffer.append(signal[ipk])
        """
        

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
