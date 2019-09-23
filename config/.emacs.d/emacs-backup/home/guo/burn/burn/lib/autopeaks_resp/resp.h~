
#ifndef BCG_RESP_H_
#define BCG_RESP_H_

#include <vector>
#include "monitor/monitor.h"
#include "signal/filter/filter.h"
#include "resp/autopeaks.h"

namespace bcg {
  namespace resp {
    using Status = bool;

    class Resp : Monitor {
    private:
      signal::LinearFilter lowpass_filter_;
      //signal::MedianFilter median_filter_;
      signal::EnergyFilter energy_filter_;
      autopeaks::AutoPeak autopeak_;
      int fs_ = 500;
      std::vector<uint64_t> peak_indices_;
      std::vector<int> peak_intervals_;
      uint64_t last_peak_index_ = 0;

    public:
      Resp() = default;
      Resp(int fs) :
	energy_filter_(fs*1.5) {
	//std::string b_path = "filter/lowpass0.4_b_" + std::to_string(fs) + ".csv";
	//std::string data_dir = "/home/guo/BCGHeart/embedding/data/";
	//std::string b_path = data_dir + "lowpass0.4_b_" + std::to_string(fs) + ".csv";
	//std::string a_path = data_dir + "lowpass0.4_a_" + std::to_string(fs) + ".csv";
	std::vector<double> B = {1.4170311532677801264943316661426564451176091097295284271240234375e-05,
				 2.834062306535560252988663332285312890235218219459056854248046875e-05,
				 1.4170311532677801264943316661426564451176091097295284271240234375e-05};
	std::vector<double> A = {1,
				 -1.9893245147329230437804881148622371256351470947265625,
				 0.98938119597905360702583266174769960343837738037109375};
	signal::LinearFilter lowpass_filter(B, A);
	lowpass_filter_ = lowpass_filter;
	float thres = 0.65;
	int min_dist = 2*fs;
	int buffer_size = 15*fs;
	autopeak_ = autopeaks::AutoPeak(thres, min_dist, buffer_size);
      }

      bool push_back(float sample) override;
    
      ~Resp() override = default;

      int num_peak_intervals() override;
      std::vector<uint64_t> peak_indices() override;
      std::vector<int> peak_intervals() override;

    };

  }
}


#endif
