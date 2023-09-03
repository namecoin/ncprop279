# Tor Prop279 Provider for Namecoin

`ncprop279` acts as a bridge between Tor Prop279 clients and Namecoin; it can be used for Namecoin naming in Tor.  Unlike `dns-prop279`, it does not use the DNS wire protocol.  This reduces attack surface and binary size, but prevents delegation from Namecoin to DNS via NS/DS records.

## Building

### Prerequisites:

Ensure you have the Go tools installed.

### Option A: Using Go get commands without Go modules

Should work on any platform with Bash; only Go 1.15-1.16.x; will not work on Go 1.17+:

1. Ensure you have the `GOPATH` environment variable set. (For those not
   familar with Go, setting it to the path to an empty directory will suffice.
   The directory will be filled with build files.)

2. To disable Go modules:

      export GO111MODULE=off

3. To retrieve the source code automatically:

      go get -d -t -u github.com/namecoin/ncprop279/...

4. To perform a necessary build step that parses ReactOS property list and applies compressed public key patch:

      go generate github.com/namecoin/certinject/...
      go generate github.com/namecoin/x509-compressed/...

6. To build the ncprop279 binary at `$GOPATH/bin/ncprop279`.

      go get -t -u github.com/namecoin/ncprop279/...

### Option B: Using Go mod commands with Go modules

Should work on any platform with Bash; Go 1.15+:

1. Clone [certinject](https://github.com/namecoin/certinject), [x509-compressed](https://github.com/namecoin/x509-compressed), and ncdns to sibling directories.

2. Install `certinject` according to its instructions.

3. Install `x509-compressed` according to its "with Go modules" instructions.

4. Setup Go modules in the ncprop279 directory:
 
       go mod init github.com/namecoin/ncdns
       go mod edit -replace github.com/coreos/go-systemd=github.com/coreos/go-systemd/v22@latest -replace github.com/namecoin/certinject=../certinject -replace github.com/namecoin/x509-compressed=../x509-compressed -replace github.com/namecoin/x509-compressed/godebug=../x509-compressed/godebug
       go mod tidy

5. Compile the ncprop279 binary in the ncprop279 directory:

       go build ./..

6. Optionally, install into `$GOPATH` at `$GOPATH/bin/ncprop279`:

       go install ./...

## Usage

You need [StemNS](https://github.com/namecoin/StemNS) or [TorNS](https://github.com/meejah/TorNS) in order to use `ncprop279`.  You also need a Namecoin lookup client such as Namecoin Core, ConsensusJ-Namecoin, or Electrum-NMC.  Your StemNS/TorNS services configuration might look like this:

```
_service_to_command = {
    "bit.onion": ['/path/to/ncprop279'],
    "bit": ['/path/to/ncprop279'],
}
```

ncprop279 can be configured by arguments on the command line (see `ncprop279 -help`)
or by the same arguments listed in a configuration file. An example configuration
file is provided in `ncprop279.conf` in this repository, uncomment and edit any
desired lines. Note that the `[ncprop279]` section header is required. This configuration
file will not be used automatically. To specify command line arguments directly
or give a path to the configuration file, append to the list in the map
mentioned above:

    "bit.onion": ['/path/to/ncprop279', '-conf=/path/to/ncprop279.conf']

## Security Notes

* `ncprop279` hasn't been carefully checked for proxy leaks.
* Using `ncprop279` will make you stand out from other Tor users.
* Stream isolation for streams opened by applications (e.g. Tor Browser) should work fine.
* Stream isolation metadata is passed through to the Namecoin lookup client, so any network traffic produced as a result of your lookups (e.g. traffic between Electrum-NMC and ElectrumX) will be stream-isolated.
* Lookups are cached; the cache is stream-isolated.
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
