
#include "heart/autopeaks.h"
#include "signal/lib/lib.h"
#include <iostream>

namespace bcg {
  namespace heart {

    std::vector<uint64_t> AutoPeaks::peak_indices() {
      std::vector<uint64_t> indices;
      for (auto index : peak_indices_)
        indices.push_back(index);
      peak_indices_.clear();
      return indices;
    }

    bool AutoPeaks::reset() {
      signal_.clear();
      peak_indices_.clear();
      return true;
    }
    
    bool AutoPeaks::findpeaks(float sample) {
      bool status = true;
      signal_index_ += 1;
      update_counter_ += 1;
      signal_.push_back(sample);
      if (!signal_.full())
	return status;
      int update_thres = signal_.size() / 2;
      if (update_counter_ < update_thres)
	return status;
      update_counter_ = 0;
      auto peaks = findpeaks_offline(signal_, threshold_, min_dist_,
				     direction_, energy_window_);
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
      //std::cerr << "after choosen peaks\n";
      for (auto peak: chosen_peaks) {
	peak_indices_.push_back(peak.peak_index);
      }
      return true;
    }

    std::vector<uint64_t> AutoPeaksWorker::peak_indices() {
      std::vector<uint64_t> indices;
      int num_indices = peak_indices_.size();
      if (num_indices > 0)
	last_peak_index_ = peak_indices_.back();
      indices.reserve(num_indices);
      for (auto index : peak_indices_)
        indices.push_back(index);
      peak_indices_.clear();
      return indices;
    }    
    
    std::vector<int> AutoPeaksWorker::peak_intervals() {
      std::vector<int> intervals;
      for (auto interval : peak_intervals_)
        intervals.push_back(interval);
      peak_intervals_.clear();
      return intervals;
    }

    float AutoPeaksWorker::loss() {
      float loss = 100000000;
      auto peak_indices = instance_.peak_indices();
      peak_indices_for_loss_.insert(peak_indices_for_loss_.end(),
				    peak_indices.begin(),
				    peak_indices.end());
      
      if (last_peak_index_ == 0) {
	int num_peak_indices = peak_indices.size();
	if (num_peak_indices > 0) {
	  last_peak_index_ = peak_indices[0];
	  peak_indices.erase(peak_indices.begin());
	} else {
	  return loss;
	}
      }
      uint64_t last_peak_index = 0;
      if (!peak_indices_.empty())
	last_peak_index = peak_indices_.back();
      else
	last_peak_index = last_peak_index_;
      
      peak_indices.insert(peak_indices.begin(), last_peak_index);
      auto new_peak_intervals = signal::diff(peak_indices);
      peak_intervals_.insert(peak_intervals_.end(),
			     new_peak_intervals.begin(),
			     new_peak_intervals.end());
      peak_indices_.insert(peak_indices_.end(),
			   peak_indices.begin(),
			   peak_indices.end());
      int num_peak_indices_for_loss = peak_indices_for_loss_.size();
      if (num_peak_indices_for_loss >= 2) {
	auto intervals = signal::diff(peak_indices_for_loss_);
	loss = signal::std(intervals);
      } 
      return loss;
    }
    
    bool AutoPeaksWorker::findpeaks(float sample) {
      return instance_.findpeaks(sample);
    }
    
    bool AutoPeaksMaster::findpeaks(float sample) {
      bool status = true;
      counter_ += 1;
      for (auto& worker : workers_) {
	worker.findpeaks(sample);
      }
      if (counter_ < counter_maximum_)
	return status;
      counter_ = 0;
      std::vector<float> losses;
      for (auto& worker: workers_)
	losses.push_back(worker.loss());
      int best_worker_ind = 0;
      float loss_min = 1000000000000;
      int num_workers = workers_.size();
      for (int ind = 0; ind < num_workers; ind++) {
	float curr_loss = losses[ind];
	if (curr_loss < loss_min) {
	  loss_min = curr_loss;
	  best_worker_ind = ind;
	}
      }
      auto best_worker = workers_[best_worker_ind];
      auto best_worker_peak_indices = best_worker.peak_indices();
      peak_indices_.insert(peak_indices_.end(),
			   best_worker_peak_indices.begin(),
			   best_worker_peak_indices.end());
      auto best_worker_peak_intervals = best_worker.peak_intervals();
      peak_intervals_.insert(peak_intervals_.end(),
			     best_worker_peak_intervals.begin(),
			     best_worker_peak_intervals.end());
      for (auto& worker : workers_) {
	worker.peak_indices();
	worker.peak_intervals();
      }
      return true;
    }
    
    std::vector<uint64_t> AutoPeaksMaster::peak_indices() {
      std::vector<uint64_t> indices;
      for (auto index : peak_indices_)
        indices.push_back(index);
      peak_indices_.clear();
      return indices;
    }
    
    std::vector<int> AutoPeaksMaster::peak_intervals() {
      std::vector<int> intervals;
      for (auto interval : peak_intervals_)
        intervals.push_back(interval);
      peak_intervals_.clear();
      return intervals;
    }


    


  }
}
