# Use an official base image
FROM docker.elastic.co/kibana/kibana:7.14.0

# Set Kibana configuration
COPY kibana.yml /usr/share/kibana/config/kibana.yml

# Expose Kibana port
EXPOSE 5601

# Run Kibana
CMD ["--config", "/usr/share/kibana/config/kibana.yml"]
