#!/bin/bash -e

#########################################################################
## Script: raspi4temps                                                 ##
## Purpose: Monitor the ARM CPU and GPU temperature of Raspberry Pi 4B ##
## Author: Philipp Michelfeit                                          ##
## Version: 1.0                                                        ##
## License: GPL v3                                                     ##
#########################################################################

# check raspberry pi version
readonly MODEL=$(tr -d '\0' </sys/firmware/devicetree/base/model | sed 's/.*/\U&/')
readonly NAME=$(echo $MODEL | cut -d' ' -f 1,2 | sed 's/ //')
readonly VERSION=$(echo $MODEL | cut -d' ' -f 3,5 | sed 's/ //')
readonly REVISION=$(echo $MODEL | cut -d' ' -f 7)

if [[ "$NAME" != "RASPBERRYPI" && "$VERSION" != "4B" ]]; then
  echo "Only compatible with Raspberry Pi 4B !"
  echo "Exiting program ..."
  exit 1
fi

# temperature constants
readonly OVERTEMPERATURE_2=85 # cpu automatically gets throttled
readonly OVERTEMPERATURE_1=80 # cpu automatically gets throttled
readonly WARNING=75

# color functions
green() {
echo -e "\\033[32;1m${@}\033[0m"
}
 
yellow() {
echo -e "\\033[33;1m${@}\033[0m"
}

red() {
echo -e "\\033[31;1m${@}\033[0m"
}

colorscale() {
  local temp=$1
  if [ $temp -ge $OVERTEMPERATURE_2 ]; then 
    red $temp
  elif [ $temp -ge $OVERTEMPERATURE_1 ]; then 
    red $temp
  elif [ $temp -ge $WARNING ]; then 
    yellow $temp
  else
    green $temp
  fi
}

# headline for the program
readonly HEADLINE="Raspberry Pi 4 temperature monitoring"

# variables for cpu and gpu temperature
soc_temp=0

cpu_clock=0
cpu_voltage=0

gpu_temp=0
gpu_clock=0

while true
do
  soc_temp=$(vcgencmd measure_temp | cut -d= -f2 | cut -d\' -f1 | cut -d. -f1)

  cpu_clock=$[$(vcgencmd measure_clock core | cut -d= -f2) / 1000 / 1000 ]
  cpu_voltage=$(vcgencmd measure_volts core | sed 's/V$//' | sed 's/^volt=//') 

  gpu_clock=$[$(vcgencmd measure_clock v3d | cut -d= -f2) / 1000 / 1000 ]

  clear  
  echo ""
  echo "  $HEADLINE"  
  echo ""
  echo "  SoC TEMPERATURE: $(colorscale $soc_temp) °C"
  echo ""
  echo "  CPU FREQUENCY:   $cpu_clock MHz"
  echo "  CPU VOLTAGE:     $cpu_voltage V"
  echo ""
  echo "  GPU FREQUENCY:   $gpu_clock MHz"
  echo ""
  sleep 1
done
