# rpi-monitoring
<div id="top"></div>

![](images/main-1.png)
[all images](https://github.com/jerome-karabenli/rpi-monitoring/tree/main/images)

<!-- ABOUT THE PROJECT -->
## About The Project

This is a bundle of monitoring tools adapted for the raspberry pi platform (arm64) and optimized by default to limit CPU and memory usage. I recommend you to use 64 bit base image instead of 32 bits, therefore you cannot install some things like kubernates, or mongodb database(the apt repo's are outdated).

I make this to learn how I can monitor and have alert if something wrong happens. I've been inspired by this project:
* [dockprom](https://github.com/stefanprodan/dockprom), It's a very complete bundle too, but not oriented specifically for RPI.


### Built With

* [Grafana](https://github.com/grafana/grafana) for visualization of your data, it's like watching your server heart beating, it's powerful and simple tool
* [Prometheus](https://github.com/prometheus/prometheus) for storage of your data (it's a serial database), Prometheus is based on pull method for scrapping data
* [Prometheus/Alertmanager](https://github.com/prometheus/alertmanager) send alerts to many recipients, I use Slack.
* [Prometheus/node_exporter](https://github.com/prometheus/node_exporter) expose machine (node) metrics to permit Prometheus to scrape them
* [Prometheus/blackbox_exporter](https://github.com/prometheus/blackbox_exporter) check a domain basic health metrics
* [cAdvisor](https://github.com/google/cadvisor) maintained by Google, export docker container metrics
* [docker-state-exporter](https://github.com/AdaptiveConsulting/docker_state_exporter) which is fork of [docker-state-exporter](https://github.com/karugaru/docker_state_exporter) - cAdvisor doesn't export containers health status, this exporter does


<!-- GETTING STARTED -->
## Getting Started
### Prerequisites

* [docker](https://docs.docker.com/get-docker/)
  ```sh
  $ curl -fsSL https://get.docker.com -o get-docker.sh
  $ sudo sh get-docker.sh
  $ sudo usermod -aG docker ${USER}
  $ sudo systemctl enable docker
  ```
* [docker-compose](https://dev.to/elalemanyo/how-to-install-docker-and-docker-compose-on-raspberry-pi-1mo)
  ```sh
  $ sudo apt-get install libffi-dev libssl-dev
  $ sudo apt install python3-dev
  $ sudo apt-get install -y python3 python3-pip
  $ sudo pip3 install docker-compose
  ```

### Installation

1. Create an slack app and get your slack api token for webhook => [slack doc](https://api.slack.com/messaging/webhooks)
2. Clone the repo
   ```sh
   git clone https://github.com/jerome-karabenli/rpi-monitoring.git
   ```
3. Paste your slack api token and your channel name in ``rpi-monitoring/alertmanager/config.yml``
   ```yaml
   global:
     resolve_timeout: 5m
     slack_api_url: '<your slack api url>'
   ```
   ```yaml
   - channel: '#<your alert channel>'
   ```
4. Set your domain name in ``rpi-monitoring/prometheus/prometheus.yml`` (optional)
   ```yml
   static_configs:
      - targets:
        - <your domain http adress>
   ```
5. Execute this command to create ``monitoring`` virtual network 
   ```sh
    docker network create monitoring
    ```
6. Execute this command in ``rpi-monitoring`` folder
    ```sh
    docker-compose up -d
    ```
7. Access to your grafana web ui from your browser on port 3000 ``http://machine-ip:3000`` default Grafana username ``admin``, password ``admin``
8. Add Prometheus as datasource => [grafana doc](https://grafana.com/docs/grafana/latest/datasources/add-a-data-source/), Prometheus url is ``http://prometheus:9090``
9. Import [grafana dashboards](https://github.com/jerome-karabenli/rpi-monitoring/tree/main/dashboards) in json format - [grafana doc](https://grafana.com/docs/grafana/latest/dashboards/export-import/#import-dashboard)



<!-- ROADMAP -->
## Roadmap

- [x] Setup prometheus with exporters
- [x] Optimize ressources usage for all services
- [x] Setup and test alertmanager for alerting
- [x] Setup dashboards
- [x] Add README in repo
- [ ] Setup ``Traefik`` as reverse proxy
- [ ] Make this more secure using ``Traefik`` middlewares
    - [ ] basic auth
    - [ ] allow some ip or dns name
    - [ ] json web token
- [ ] Why not testing this with [podman](https://podman.io/) seems to be better than docker, more secure, more lightweight, integrating pod concepts etc..


See the [open issues](https://github.com/jerome-karabenli/rpi-monitoring/issues) for a full list of proposed features (and known issues).


<!-- CONTRIBUTING -->
## Contributing

Contributions are what make the open source community such an amazing place to learn, inspire, and create. Any contributions you make are **greatly appreciated**.

If you have a suggestion that would make this better, please fork the repo and create a pull request. You can also simply open an issue with the tag "enhancement".
Don't forget to give the project a star! Thanks again!

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request


<!-- LICENSE -->
## License

Distributed under the MIT License. See `LICENSE.txt` for more information.



<!-- CONTACT -->
## Contact

Jerome Karabenli - jkarabenli.dev@gmail.com

Project Link: [https://github.com/jerome-karabenli/rpi-monitoring](https://github.com/jerome-karabenli/rpi-monitoring)




<!-- ACKNOWLEDGMENTS -->
## Acknowledgments

* [Alertmanager alerts exemple](https://awesome-prometheus-alerts.grep.to/rules.html)
* [cAdvisor options](https://github.com/google/cadvisor/blob/master/docs/runtime_options.md)
* [Create customized slack alert notification](https://hodovi.cc/blog/creating-awesome-alertmanager-templates-for-slack/)


<!-- MORE -->
## More

I also use for my personal use case

* [nodeJS exporter](https://www.npmjs.com/package/express-prom-bundle) in this way I can monitor my express app's.
* [docker autoheal](https://hub.docker.com/r/willfarrell/autoheal/) permit to restart container if health checks fail (by default docker do nothing in this case, only docker swarm do it or kubernates by default)

## Infos
For ``docker-state-exporter`` I recompile the original binaries with any changes from [repo](https://github.com/AdaptiveConsulting/docker_state_exporter) to have compatibility for arm64. In compose file, the target for this image is my GitHub container registry. You can if you want to build the same image yourself and change compose file. 
I also use the forked repo because Dockerfile in original repo doesn't work for me and in forked repo they add an interesting option (don't keep docker labels) who permit another optimization


For ``cAdvisor`` I tried to recompile original binaries from Google's repo, but it's not working, I do not have enough knowledge to achieve this. I found an alternative [unibaktr/cadvisor](https://hub.docker.com/r/unibaktr/cadvisor)

