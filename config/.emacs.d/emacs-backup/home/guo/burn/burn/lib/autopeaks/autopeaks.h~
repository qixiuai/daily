#ifndef RESP_AUTOPEAKS_H_
#define RESP_AUTOPEAKS_H_

#include <algorithm>
#include <cmath>
#include <iostream>
#include <fstream>
#include <vector>
#include <string>
#include <numeric>
#include <memory>
#include <cstdlib>

#include "resp/findpeaks.h"

namespace bcg {
  
  namespace autopeaks {

    class AutoPeak {
    public:
      AutoPeak() {}
      AutoPeak(float threshold, int min_dist, int buffer_size):
	threshold_{threshold}, min_dist_{min_dist}, buffer_size_{buffer_size} {
	  signal_ = Buffer<float>(buffer_size);
	}
      bool findpeaks(float sample);
      std::vector<uint64_t> peak_indices();
      
    private:
      float threshold_;
      int min_dist_;
      int buffer_size_;
      Buffer<float> signal_;
      std::vector<uint64_t> peak_indices_;

      uint64_t signal_index_ = 0;
      uint32_t update_counter_ = 0;
    };

  } 

}

#endif
