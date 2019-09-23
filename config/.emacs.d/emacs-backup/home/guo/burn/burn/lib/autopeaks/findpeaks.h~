
#ifndef RESP_FINDPEAKS_H
#define RESP_FINDPEAKS_H

#include <set>
#include <vector>
#include "boost/circular_buffer.hpp"

namespace bcg {

  namespace autopeaks {

    template <typename T>
    using Buffer = boost::circular_buffer<T>;
    
    struct Peak {
      uint64_t peak_index;
      float peak_value;
      Peak(int peak_index, float peak_value) {
	this->peak_index = peak_index;
	this->peak_value = peak_value;
      }
    };
    
    std::vector<Peak> findpeaks_offline(const Buffer<float>& signal, float threshold, int min_dist);
    

    
  }
}

#endif
