# Tor Prop279 Provider for Namecoin

`ncprop279` acts as a bridge between Tor Prop279 clients and Namecoin; it can be used for Namecoin naming in Tor.  Unlike `dns-prop279`, it does not use the DNS wire protocol.  This reduces attack surface and binary size, but prevents delegation from Namecoin to DNS via NS/DS records.

## Usage

You need [StemNS](https://github.com/namecoin/StemNS) or [TorNS](https://github.com/meejah/TorNS) in order to use `ncprop279`.  You also need a Nameecoin lookup client such as Namecoin Core, ConsensusJ-Namecoin, or Electrum-NMC.  Your StemNS/TorNS services configuration might look like this:

~~~
_service_to_command = {
    "bit.onion": ['/path/to/ncprop279'],
    "bit": ['/path/to/ncprop279'],
}
~~~

## Security Notes

* `ncprop279` hasn't been carefully checked for proxy leaks.
* Using `ncprop279` will make you stand out from other Tor users.
* Stream isolation for streams opened by applications (e.g. Tor Browser) should work fine.  However, stream isolation metadata won't propagate to streams opened by the Namecoin lookup client.  That means you should only use `ncprop279` with a Namecoin name lookup client that will not generate outgoing traffic when you query it, e.g. a full-block-receive Namecoin node such as Namecoin Core or libdohj-namecoin in leveldbtxcache mode.  Electrum-NMC is not a good idea.
* `ncprop279` probably caches lookups.  This could be used to fingerprint users.
* This whole thing is highly experimental!  Please test it and give feedback, but **don't rely on it behaving correctly**.

## License

ncprop279 is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

ncprop279 is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with ncprop279.  If not, see [https://www.gnu.org/licenses/](https://www.gnu.org/licenses/).

ncprop279 is produced independently from the Tor® anonymity software and carries no guarantee from The Tor Project about quality, suitability or anything else.
