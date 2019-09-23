
#ifndef HEART_HEART_H_
#define HEART_HEART_H_

#include <vector>
#include <cstdint>

#include "signal/filter/filter.h"
#include "heart/autopeaks.h"


namespace bcg {
  namespace heart {

    class Heart {
    public:
      Heart() = default;
      Heart(int fs=500);
      bool push_back(float sample);
      std::vector<uint64_t> peak_indices();
      std::vector<int> peak_intervals();
      
    private:
      signal::LinearFilter bandpass_filter_;
      AutoPeaksMaster master_;
    };

    
  }
}


#endif

