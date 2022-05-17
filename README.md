# blocking_reader
A C++ class that allows the caller to perform blocking reads from boost::asio::serial_port with configured timeouts in milliseconds.

The boost serial IO library allows you to perform a blocking read on the serial port, but it is quite difficult to perform a blocking read with a timeout, this class **blocking_reader** wraps the port object and provides a blocking read with a millisecond timeout on the port.

More details can be found [here](http://www.ridgesolutions.ie/index.php/2012/12/13/boost-c-read-from-serial-port-with-timeout-example/):

Example, to read from COM3, character by character with a 500ms timeout on each read.

```cpp
#include <boost/asio/serial_port.hpp>
#include <boost/asio.hpp>
#include "blocking_reader.h"

using namespace boost;

std::string read_response() {
    asio::io_service io;
    asio::serial_port port(io);

    port.open("COM3");
    port.set_option(asio::serial_port_base::baud_rate(115200));

    // A blocking reader for this port that
    // will time out a read after 500 milliseconds.
    blocking_reader reader(port, 500);
    
    char c;
    std::string rsp;
    
    // read from the serial port until we get a
    // \n or until a read times-out (500ms)
    while (reader.read_char(c) && c != '\n') {
        rsp += c;
    }
    
    if (c != '\n') {
        // it must have timed out.
        throw std::exception("Read timed out!");
    }
    
    return rsp;
}

```
