# perl-parsing_hardware_interface
perl in ASIC design
this script is used to parse the verilog file(top.v) and get the port information which contains the port name, I/O and width.

perl hardware_int.pl top.v

Code :
#!/usr/bin/perl
$in=$ARGV[0];
if(!defined $in) {
    die "Usage:$0 filename";
}

my $out=$in;
$out=~s/(\.\w+)?$/.csv/;

if(!open $in_fh, '<',$in) {
    die "cannot open '$in':$!";
}

if(!open $out_fh, '>',$out) {
    die "cannot open '$out':$!";
}

print $out_fh "Name,    I/O,    Width\n";
while(<$in_fh>)  {
    chmop;
    if(/^\s*input/) {
        my @line_i = split(',',$_);
        my @line_i1 = split(' ',$line_i[0]);
        my $line_width = $line_i1[1];
        if($line_width=~ /^\[/) {
            $width = $line_width;
            $width =~ /(\[)(.*):(.*)(\])/;
            my ($high,$low)=($2,$3);
            my $wid = $high-$low+1;
            print $out_fh "$line_i1[2],"."    "."I,"."    "."$wid"."\n";
        }
        else {
            print $out_fh "$line_i1[1],"."    "."I,"."    "."1"."\n";
        }
    }
    elsif(/^\s*output/) {
        my @line_o = split(',',$_);
        my @line_o1 = split(' ',$line_o[0]);
        my $line_width_1 = $line_o1[1];
        if($line_width_1=~ /^\[/) {
            $width_1 = $line_width_1;
            $width_1 =~ /(\[)(.*):(.*)(\])/;
            my ($high_1,$low_1)=($2,$3);
            my $wid_1 = $high_1-$low_1+1;
            print $out_fh "$line_o1[2],"."    "."O,"."    "."$wid_1"."\n";
        }
        else {
            print $out_fh "$line_o1[1],"."    "."O,"."    "."1"."\n";
        }
    
    }
}


