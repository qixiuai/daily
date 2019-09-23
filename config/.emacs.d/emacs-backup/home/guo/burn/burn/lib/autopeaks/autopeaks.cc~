
#include "resp/autopeaks.h"
#include "resp/findpeaks.h"

namespace bcg {

  namespace autopeaks {

    bool AutoPeak::findpeaks(float sample) {
      bool status = true;
      signal_index_ += 1;
      update_counter_ += 1;
      signal_.push_back(sample);
      if (!signal_.full())
	return status;
      auto update_thres = signal_.size() / 2;
      if (update_counter_ < update_thres)
	return status;
      update_counter_ = 0;
      auto peaks = findpeaks_offline(signal_, threshold_, min_dist_);
      uint64_t index_offset = signal_index_ - signal_.size() + 1;
      auto chosen_index_start = signal_.size() / 4;
      auto chosen_index_end = signal_.size() - signal_.size() / 4;

      std::vector<Peak> chosen_peaks;
      auto choose_peaks = [&](std::vector<Peak> peaks) {
	for (auto peak : peaks) {
	  uint64_t peak_index = peak.peak_index;
	  if (index_offset == 0 && peak_index < chosen_index_end) {
	    chosen_peaks.push_back(peak);
	    continue;
	  }
	  if (peak_index < chosen_index_start or peak_index >= chosen_index_end)
	    continue;
	  peak_index += index_offset;
	  peak.peak_index = peak_index;
	  chosen_peaks.push_back(peak);
	}
      };
      choose_peaks(peaks);
      
      for (auto peak: chosen_peaks) {
	peak_indices_.push_back(peak.peak_index);
      }
      return true;
    }

    std::vector<uint64_t> AutoPeak::peak_indices() {
      std::vector<uint64_t> indices;
      for (auto index : peak_indices_)
	indices.push_back(index);
      peak_indices_.clear();
      return indices;
    }
    
  }
}
