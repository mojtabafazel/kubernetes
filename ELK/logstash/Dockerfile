# Use an official base image
FROM docker.elastic.co/logstash/logstash:7.14.0

# Set the Logstash configuration file
COPY logstash.conf /usr/share/logstash/pipeline/logstash.conf

# Install any additional plugins if needed
# RUN logstash-plugin install <plugin-name>

# Expose the Logstash port
EXPOSE 5044

# Run Logstash
CMD ["-f", "/usr/share/logstash/pipeline/logstash.conf"]
