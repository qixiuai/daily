
#include "findpeaks.h"


namespace bcg {
  
  namespace autopeaks {

    std::vector<Peak> findpeaks_offline(const Buffer<float>& signal, float threshold, int min_dist) {
      int num_samples = signal.size();
      float signal_max = *std::max_element(signal.begin(), signal.end());
      float signal_min = *std::min_element(signal.begin(), signal.end());
      float thres = threshold * (signal_max - signal_min) + signal_min;
      
      std::vector<Peak> raw_peaks;
      for (int ind = 1; ind < num_samples - 1; ind++) {
	if (signal[ind] >= signal[ind-1] &&
	    signal[ind] >= signal[ind+1] &&
	    signal[ind] > thres) {
	  int peak_index = ind;
	  float peak_value = signal[ind];
	  //TODO  try emplace_back
	  raw_peaks.push_back(Peak(peak_index, peak_value));
	}
      }

      if (raw_peaks.size() <= 1 || min_dist <= 1) {
	return raw_peaks;
      }
      
      std::vector<Peak> raw_peaks_sorted(raw_peaks.begin(), raw_peaks.end());
      std::sort(raw_peaks_sorted.begin(), raw_peaks_sorted.end(), [](Peak p1, Peak p2) {
	  return p1.peak_value > p2.peak_value;
	});

      auto masks = std::vector<bool>(num_samples, true);
      for (const auto peak: raw_peaks_sorted) {
	int peak_index = peak.peak_index;
	if (masks[peak_index]) {
	  auto sl_st = std::max(0, peak_index - min_dist);
	  auto sl_ed = std::min(num_samples - 1, peak_index + min_dist);
	  for (int ind = sl_st; ind <= sl_ed; ind++)
	    masks[ind] = false;
	  masks[peak_index] = true;
	}
      }
      std::vector<Peak> peaks;
      for (const auto& peak : raw_peaks) {
	int peak_index = peak.peak_index;
	if (masks[peak_index])
	  peaks.push_back(peak);
      }
      return peaks;
    }
  }
}
