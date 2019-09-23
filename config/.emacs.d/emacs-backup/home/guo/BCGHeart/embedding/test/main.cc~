
#include <stdexcept>
#include <iostream>
#include <fstream>
#include <string>
#include <vector>

using Status = bool;

Status load_bcg(std::vector<float>& signal, std::string filepath) {
  bool flag = true;
  std::ifstream in(filepath);
  if (!in.is_open()) {
    throw std::invalid_argument(filepath + " is not good!");
  }
  
  std::string line;
  while (std::getline(in, line)) {
    float value = std::stof(line);
    signal.push_back(value);
  }
  
  return flag;
}

int main() {
  std::vector<float> bcg;
  std::string data_dir = "/home/guo/BCGHeart/embedding/";
  std::string filename ="raw_bcg.txt";
  Status status = load_bcg(bcg, data_dir+filename);
  if (!status) {
    std::cerr << "something wrong with load_bcg\n";
  }
  std::cout << bcg.size() << '\n';
  
  return 0;
}

