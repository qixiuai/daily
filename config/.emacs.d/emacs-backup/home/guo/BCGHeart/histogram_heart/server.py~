
from flask import Flask, request
from flask_restful import Api, Resource, reqparse

import requests

import pdb

from ecgpeaks import findpeaks_in_ecg

app = Flask(__name__)
api = Api(app)


class ECGPeaks(Resource):

    def post(self):
        signal = request.json['signal']
        if type(signal) is str:
            signal = list(map(int, signal.split(',')))
        peak_indices, peak_values = findpeaks_in_ecg(signal)
        peak_indices = ",".join(list(map(str, peak_indices)))
        peak_values = ",".join(list(map(str, peak_values)))
        return {"peak_indices": peak_indices, "peak_values": peak_values}

    def get(self):
        return "get demo for test usage"

api.add_resource(ECGPeaks, "/")


if __name__ == '__main__':
    app.run('127.0.0.1', port=8001)

