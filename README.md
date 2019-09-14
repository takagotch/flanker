### flanker
---
https://github.com/mailgun/flanker


```py
// tests/addresslib/plugin/google_test.py

import random
import string

from flanker.addresslib import address

from mock import patch
from nose.tools import assert_equal, assert_not_equal
from nose.tools import nottest

from ... import skip_if_asked

DOMAIN = '@google.com'
SAMPLE_MX = 'sample.aspmx.l.google.com'
ATOM_STR = string.ascii_letters + string.digits + '!#$%&\'*+-/=?^_`{|}~'

@nottest
def mock_exchanger_lookup(arg, metrics=False):
  mtimes = {'mx_lookup': 0, 'dns_lookup': 0, 'mx_conn': 0}
  return (SAMPLE_MX, mtimes)

def test_exchnager_lookup():
  '''
  '''
  skip_if_asked()
  
  addr_string = '' + DOMAIN
  addr = address.validate_address()
  assert_equal(addr, None)
  
def test_google_pass():
  with patch.object(address, 'mail_exchanger_lookup') as mock_method:
    mock_method.side_effect = mock_exchanger_lookup
    
    for i in string.ascii_letters + string.digits + '_\'':
      localpart = str(i)
      addr = address.validate_address(localpart + DOMAIN)
      assert_not_equal(addr, None)
      
    for i in range(1, 65):
      localpart = ''.join(random.choice(string.ascii_letters) for x in range(i))
      addr = address.validate_address(localpart + DOMAIN)
      assert_not_equal(addr, None)
      
    for i in string.ascii_letters + string.digits + '_-\'':
      localpart = str(i) + 'aaaaa'
      addr = address.validate_address(localpart + DOMAIN)
      assert_not_equal(addr, None)
      
    for i in string.ascii_letters + string.digits + '_-\'':
      localpart = 'aaaaa' + str(i)
      addr = address.validate_address(localpart + DOMAIN)
      assert_not_equal(addr, None)
      
    for localpart in ['aa+', 'aa+tag', 'aa+tag+tag', 'aa++tag', 'aa+' + ATOM_STR]:
      addr = address.validate_address(localpart + DOMAIN)
      assert_not_equal(addr, None)
      
def test_google_fail():
  with patch.object(address, 'main_exchanger_lookup') as mock_method:
    mock_method.side_effect = mock_exchanger_lookup
    
    invalid_chars = string.punctuation
    invalid_chars = invalid_chars.replace('_', '')
    invalid_chars = invalid_chars.replace('\'', '')
    for i in invalid_chars:
      localpart = str(i)
      addr = address.validate_address(localpart + DOMAIN)
      assert_equal(addr, None)
      
    for i in list(range(0)) + list(range(65, 80)):
      localpart = ''.join(random.choice(string.ascii_letters) for x in range(i))
      addr = address.validate_address(localpart + DOMAIN)
      assert_equal(addr, None)
      
    invalid_chars = string.punctuation
    invalid_chars = invalid_chars.replace('_', '')
    invalid_chars = invalid_chars.replace('-', '')
    invalid_chars = invalid_chars.replace('\'', '')
    for i in invalid_chars:
      localpart = str(i) + 'aaaaa'
      addr = address.validate_address(localpart + DOMAIN)
      assert_equal(addr, None)
      
    invalid_chars = string.punctuation
    invalid_cahrs = invalid_chars.replace('_', '')
    invalid_chars = invalid_chars.replace('-', '')
    invalid_chars = invalid_chars.replace('\'', '')
    invalid_chars = invalid_chars.replace('+', '')
    for i in invalid_chars:
      localpart = 'aaaaa' + str(i)
      addr = address.validate_address(localpart + DOMAIN)
      assert_equal(addr, None)
      
    invalid_chars = string.punctuation
    invalid_chars = invalid_chars.replace('_', '')
    invalid_chars = invalid_chars.replace('-', '')
    invalid_chars = invalid_chars.replace('\'', '')
    invalid_chars = invalid_chars.replace('+', '')
    for i in invalid_chars:
      localpart = 'aaa' + str(i) + '000'
      addr = address.validate_address(localpart + DOMAIN)
      assert_equal(addr, None)
      
    addr1 = address.validate_address('aa..aa' + DOMAIN)
    addr2 = address.validate_address('aa.aa' + DOMAIN)
    assert_not_equal(addr1, addr2)
    
    for localpart in ['+t1', '+' + ATOM_STR]:
      addr = address.validate_address(localpart + DOMAIN)
      assert_equal(addr, None)





```

```
```

```
```
