# INCIT-EV DSS

Welcome to the main repository of the INCIT-EV DSS project.
In this `README.md`, you will find every important information on how to set up and make this backend running on your local machine.

## Developing

### Requirements

- Python 3+
- Pip
- Pipenv (`pip install --user pipenv`)

### Instructions

In this section, we propose a guideline on how to setup a development environment to make changes and test new features in the DSS.

1. Clone this repository and set the root folder as your working directory:

```shell script
git clone https://zenit.linksfoundation.com/incit-ev/dss.git
cd dss
```

2. Install all required dependencies

```shell script
pipenv install
```

3. Bring up your MongoDB database cluster (if you do not have one yet):

```shell script
cd docker
docker-compose up -d
cd ..
```

4. Setup your configuration inside `app/core/config.py`

5. Start the backend

```shell script
uvicorn app.main:app --reload
```

6. Visit `http://localhost:8000/api/v1/docs` to see the OpenAPI interactive documentation.

## Testing for ATOS

### Requirements

- docker

### Instructions

In this section, we propose a guideline on how to setup a fixed environment to understand API's functionality in order to facilitate the construction of the front-end of the DSS.

1. Clone this repository and set the root folder as your working directory:

```shell script
git clone https://zenit.linksfoundation.com/incit-ev/dss.git
cd dss/docker
```

2. Perform following command to access in Links machine where the latest backend images are present:

```shell script
docker login maestri.ismb.it:5050
Username: <Sent on email>
Password: <Sent on email>
```

3. Locate in docker folder of the project and download backend images:

```shell script
cd docker
docker-compose up -d
cd ..
```

wait until the download is complete

4. Images are available in you local environment and you can test them to understand the logic of DSS backend. You have 3 differents images:

   - Empty database (Mongo Express), located at: 0.0.0.0:28000
   - API (Swagger UI), located at: 0.0.0.0:5000/docs
   - Simulator (Swagger UI), located at: 0.0.0.0:9000/docs

### Database initialization

To start testing the backend it's important to initialize your database (at the beginning it's empty) in the right way.

In the `/docker` folder, there's a `mongo-seed` directory: this will contain a sub folder for each mongodb collection that will be needed to initialized and seeded as dependency. In each sub-folder there are two files: one `Dockerfile` with the instructions to seed the mongo database and a `.json`, with the actual data to be inserted.

For now, there are two collections that are needed to be seeded: "users" and "defaults" (which stores both the defaults for the analyses inputs and dcm weights).

From the frontend point of view, these can be ignored: both the posting of analyses requests and dcm requests will only need the required fields (fewer, now) that are consultable in the interactive docs of the API (Swagger UI), located at: 0.0.0.0:5000/docs. The backend will take care of merging the input from the frontend-user with the latest defaults.

Check the `/docker/docker-compose.yaml` to understand how these files are used.

### Users collections

The users collection will be automatically seeded to have one single document in which the simulator user will be present. This will not affect user creation, as each new user will simply be placed next to the seeded one.

```js
{
    _id: ObjectId('6343dc262a2374f885d4e023'),
    username: 'dss-integration',
    email: 'integration@linksfoundation.com',
    name: 'dss-integration',
    surname: 'ex',
    hashed_password: '$2b$12$sirbD1h8eAqAK.kLz4ScZekeRWHunydOnvE29ugQSyI9.K6WOBQTu',
    scopes: [
        'simulator'
    ]
}
```

- password (in clear): Simulator!2022!

### Defaults collections

The defaults collection will only ever have one document, which will be the latest version of the defaults.
The latest defaults are the following:

<details>
  <summary>Toggle to see defaults</summary>

```json
[
  {
    "analysis": {
      "power": {
        "Expected_EV": 1,
        "ROIG": 100000,
        "Solar_PP_profile": [
          0, 0, 0, 0, 0, 0, 0.07347, 0.068571, 0.261224, 0.462857, 0.611429,
          0.706122, 0.755918, 0.788571, 0.763265, 0.68898, 0.570612, 0.412245,
          0.219592, 0.05551, 0.022857, 0.000816, 0, 0
        ],
        "Electricity_cost_profile": [
          0.1, 0.1, 0.1, 0.1, 0.1, 0.1, 0.1, 0.12, 0.12, 0.12, 0.12, 0.12, 0.12,
          0.12, 0.12, 0.12, 0.12, 0.12, 0.12, 0.1, 0.1, 0.1, 0.1, 0.1
        ],
        "PPP": [
          85.837, 63.718, 64.118, 72.512, 80.333, 108.701, 110.754, 116.063,
          143.6227, 126.257, 76.2783, 56.9315, 34.9557, 45.1908, 49.384,
          137.7852, 123.9376, 190.2375, 190.046, 168.919, 145.009, 175.94,
          129.545, 82.512
        ]
      },
      "ci": {
        "Expected_EV": 1,
        "ROIG": 100000
      },
      "ubm": {
        "input": {
          "bevs_ratio": 0.05,
          "phevs_ratio": 0
        },
        "config": {
          "night_ratio": {
            "home_public": 0.3,
            "home_private": 0.35
          },
          "day_ratio": {
            "work_public": 0.15,
            "work_private": 0.15,
            "other_public": 0.05,
            "other_semi_public": 0,
            "fast": 0
          },
          "home_parking_time": {
            "6": 0.1,
            "7": 0.2,
            "8": 0.4,
            "9": 0.2,
            "10": 0.1
          },
          "work_parking_time": {
            "6": 0.1,
            "7": 0.2,
            "8": 0.4,
            "9": 0.2,
            "10": 0.1
          },
          "other_parking_time": {
            "1": 0.4,
            "2": 0.2,
            "3": 0.2,
            "4": 0.1,
            "5": 0.1
          },
          "starting_soc_dist": {
            "25": 0.1177,
            "50": 0.1175,
            "0,00": 0.0058,
            "8,33": 0.0357,
            "16,66": 0.0851,
            "33,33": 0.1193,
            "41,66": 0.1105,
            "58,33": 0.1236,
            "66,66": 0.0948,
            "75,00": 0.0654,
            "83,33": 0.058,
            "91,66": 0.0388,
            "100,00": 0.0278
          },
          "final_soc_dist": {
            "25": 0.0063,
            "50": 0.02,
            "0,00": 0.0001,
            "8,33": 0.0011,
            "16,66": 0.0034,
            "33,33": 0.007,
            "41,66": 0.0118,
            "58,33": 0.0253,
            "66,66": 0.035,
            "75,00": 0.0713,
            "83,33": 0.0712,
            "91,66": 0.0517,
            "100,00": 0.6958
          },
          "starting_time_night": {
            "0": 0.0863,
            "1": 0.0458,
            "2": 0.1102,
            "3": 0.0083,
            "4": 0.0069,
            "5": 0.0114,
            "6": 0.0167,
            "7": 0.0588,
            "20": 0.2517,
            "21": 0.1563,
            "22": 0.1352,
            "23": 0.1124
          },
          "starting_time_day": {
            "8": 0.113,
            "9": 0.097,
            "10": 0.088,
            "11": 0.076,
            "12": 0.083,
            "13": 0.097,
            "14": 0.092,
            "15": 0.086,
            "16": 0.073,
            "17": 0.069,
            "18": 0.062,
            "19": 0.064
          },
          "starting_time_night_home": {
            "0": 0.0311,
            "1": 0.0165,
            "2": 0.0397,
            "3": 0.003,
            "4": 0.0025,
            "5": 0.0041,
            "6": 0.006,
            "7": 0.0212,
            "8": 0.0404,
            "9": 0.0354,
            "10": 0.0392,
            "11": 0.0371,
            "12": 0.0403,
            "13": 0.0416,
            "14": 0.046,
            "15": 0.04,
            "16": 0.055,
            "17": 0.0677,
            "18": 0.0961,
            "19": 0.1009,
            "20": 0.0907,
            "21": 0.0563,
            "22": 0.0487,
            "23": 0.0405
          },
          "starting_time_day_work": {
            "0": 0.0014,
            "1": 0.0012,
            "2": 0.0,
            "3": 0.0,
            "4": 0.0,
            "5": 0.0076,
            "6": 0.018,
            "7": 0.0723,
            "8": 0.1431,
            "9": 0.0808,
            "10": 0.0562,
            "11": 0.0585,
            "12": 0.0699,
            "13": 0.0681,
            "14": 0.1085,
            "15": 0.0853,
            "16": 0.0686,
            "17": 0.0315,
            "18": 0.0203,
            "19": 0.0131,
            "20": 0.0074,
            "21": 0.0828,
            "22": 0.0025,
            "23": 0.0029
          },
          "starting_time_day_other": {
            "0": 0.0115,
            "1": 0.0081,
            "2": 0.0047,
            "3": 0.0039,
            "4": 0.0025,
            "5": 0.0058,
            "6": 0.0174,
            "7": 0.0519,
            "8": 0.0878,
            "9": 0.0755,
            "10": 0.0685,
            "11": 0.059,
            "12": 0.0648,
            "13": 0.0751,
            "14": 0.0714,
            "15": 0.0668,
            "16": 0.0567,
            "17": 0.0532,
            "18": 0.0478,
            "19": 0.05,
            "20": 0.0475,
            "21": 0.0337,
            "22": 0.0211,
            "23": 0.0153
          },
          "km_travelled_dist": {
            "10": 0.1729,
            "20": 0.2751,
            "30": 0.2121,
            "50": 0.1991,
            "75": 0.0886,
            "100": 0.0262,
            "150": 0.0168,
            "200": 0.0092
          }
        }
      }
    },
    "dcm": {
      "weights": {
        "ratio": {
          "asc_1": 0.724,
          "asc_2": 0.519,
          "asc_3": 0.878,
          "asc_4": 0.832,
          "asc_5": 0.0221,
          "b_citysize": 0.0692,
          "b_diffusion": 0.0214,
          "b_opercost": -0.0412,
          "b_price": -0.137,
          "b_purchincent": 0.0274,
          "b_range": 0.0766,
          "b_utilincent": 0.028,
          "mu_bev": 1,
          "mu_noevs": 1.94,
          "mu_phev": 1
        },
        "charging": {
          "asc_1": -0.0346,
          "asc_2": 0.0974,
          "asc_3": 0.295,
          "asc_4": 0.0697,
          "asc_5": 0,
          "b_ancillary_services": 0.133,
          "b_charging_price": -0.121,
          "b_renewable_energy": 0.175,
          "b_waiting_time": -0.0517,
          "mu_home": 1.84,
          "mu_work": 1.2
        }
      }
    }
  }
]
```

</details>

### Updating defaults

To update the defaults, edit the `defaults.json` file present in `/docker/mongo-seed/seed-defaults/`. To keep record of changes, also update this `readme.md` document with the latest changes.

Don't forget to also update the models present in the [dss data model repo](https://zenit.linksfoundation.com/incit-ev/data-model).

## Production

> Coming soon...
