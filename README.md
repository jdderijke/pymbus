# MbusTcpMaster
This project aims to develop a generic Mbus master for reading data from Mbus devices.

## Standards
IEC 870-5, EN1434-3

## Connection
For now only the Tcp connection is implemented

## Supported Telegrams
For now only the REQ_UD2 → RSP_UD Request/Respond Procedure with Variable Data Structure is implemented.

## Methods
**MbusTcpMaster.__init__:**   
<code>
<ins>usage:</ins> result = MbusTcpMaster(host, port, [name, auto_connect])  

<ins>args:</ins>  
host: IP address of TCP/Mbus bridge  
port: Port 

<ins>kwargs:</ins>  
name: Name for this instance (str:'')  
auto_connect: Connect after initialization (bool:True)  

<ins>returns:</ins>  Initialized connection    
</code>

**scan_slaves_primary:**  
<code>
	slaves = test.scan_slaves_primary([scan_timeout, stop_at])  
<ins>kwargs:</ins>  
	scan_timeout:	(float:1.0)	How long to wait for response from an address  
	stop_at:	(int:250)	Quit looking for more slaves after this number of detected slaves  
<ins>returns:</ins>   
	A dictionary with Fixed Data Headers (FDH's) part of the response of the detected slaves, keyed on their primary addresses.  
	An FDH contains: Ident. Nr. Manufr. Version Medium AccessNo. Status Signature  
</code>

**get_all_fields:**  
<code>
        result = test.get_all_fields(slave_address, [extensive_mode, scale_results])  
<ins>args:</ins>  
        slave_address:  (int:1)         slave address to send request to  
<ins>kwargs:</ins>  
        extensive_mode: (bool:False)    generate extra field information in the 'fields' part of the result  
        scale_results:  (bool:True)     Return scaled values  
<ins>returns:</ins>  
        All fields/registers from 1 specific slave address. (only VARIABLE DATA STRUCTURE is supported at this moment)  
        returns a dictionary with the FDH information of this slave and a 'fields' key  
        The 'fields' key contains a list of dictionaries (1 per decoded field/register) with: Description, Value, Unit  
</code>

## How to use
Using the MbusTcpMaster the 'look' and 'feel' should be similar to using the ModbusTcpClient from the pymodbus package

Example:

<code>
from MbusTcpMaster import MbusTcpMaster


# Create an instance of the MbusTcpMaster (replace ip_address and port with the appropriate values)
test = MbusTcpMaster(host='ip_address', port=port)

# get all slaves on the bus (this may take a while to scan... so we limit the maximum to 2)
slaves = test.scan_slaves_primary(stop_at=2)

if slaves:
    for slave_address in slaves:
        # get all the fields/registers from the slave
        results = test.get_all_fields(slave_address=slave_address)
        
        for field in results['fields']:
            print(' '.join(f'{j}' for i,j in field.items()))
            
        print()

# close the connection
test.close()
</code>


