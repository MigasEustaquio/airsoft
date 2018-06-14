package com.airsoft.miguel.airsoft.activity;

import android.content.DialogInterface;
import android.content.Intent;
import android.content.SharedPreferences;
import android.support.v7.app.AlertDialog;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.support.v7.widget.Toolbar;
import android.view.Menu;
import android.view.MenuInflater;
import android.view.MenuItem;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
import android.widget.Toast;

import com.airsoft.miguel.airsoft.R;
import com.airsoft.miguel.airsoft.config.ConfiguracaoFirebase;
import com.google.firebase.auth.FirebaseAuth;

import java.text.DecimalFormat;

public class MainActivity extends AppCompatActivity {

    private TextView nK;
    private TextView nD;
    private TextView nV;
    private TextView rK;
    private TextView rD;
    private TextView rV;
    private TextView kd;
    private float kill=0, death=0;
    private int vit=0, jogos=0;
    private Bundle extra=null;
    private EditText nome;
    private String nomeU;
    private Toolbar toolbar;

    private FirebaseAuth autenticacao;

    private static final String ARQUIVO_PREFERENCIA = "ArquivoPreferencia";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        nK = (TextView) findViewById(R.id.NKID);
        nD = (TextView) findViewById(R.id.NDID);
        nV = (TextView) findViewById(R.id.NVID);
        rK = (TextView) findViewById(R.id.PKID);
        rD = (TextView) findViewById(R.id.PDID);
        rV = (TextView) findViewById(R.id.PVID);
        kd = (TextView) findViewById(R.id.kdID);


        toolbar = (Toolbar) findViewById(R.id.toolbar);

        SharedPreferences sharedPreferences = getSharedPreferences(ARQUIVO_PREFERENCIA, 0);

        nomeU = "Inisra seu Nome";

        kill = sharedPreferences.getFloat("kill", 0);
        death = sharedPreferences.getFloat("death", 0);
        vit = sharedPreferences.getInt("vic", 0);
        jogos = sharedPreferences.getInt("jogos", 0);
        if (sharedPreferences.contains("nome"))
            nomeU = sharedPreferences.getString("nome", "Inisra seu Nome");

        extra = getIntent().getExtras();

        if (extra != null) {
            if (extra.getInt("jogos") != 0) {
                kill = extra.getFloat("kill");
                death = extra.getFloat("death");
                vit = extra.getInt("vit");
                jogos = extra.getInt("jogos");

                extra = null;
            }
        }

        DecimalFormat df = new DecimalFormat("0.###");

        toolbar.setTitle(nomeU);
        setSupportActionBar(toolbar);
        nK.setText("" + kill);
        nD.setText("" + death);
        nV.setText("" + vit);
        if (death!=0)
            kd.setText("" + kill/death);
        else kd.setText("" + kill);
        if(jogos!=0) {
            rK.setText("" + df.format(kill/jogos));
            rD.setText("" + df.format(death/jogos));
            rV.setText("" + df.format((vit*100)/jogos) +"%");
            }

        SharedPreferences.Editor editor = sharedPreferences.edit();

        editor.putFloat("kill", kill);
        editor.putFloat("death", death);
        editor.putInt("vic", vit);
        editor.putInt("jogos", jogos);
        editor.commit();

        nK.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Toast.makeText(MainActivity.this, "Número total de kills", Toast.LENGTH_LONG).show();
            }
        });

        nD.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Toast.makeText(MainActivity.this, "Número total de mortes", Toast.LENGTH_LONG).show();
            }
        });

        nV.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Toast.makeText(MainActivity.this, "Número total de vitórias", Toast.LENGTH_LONG).show();
            }
        });

        rK.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Toast.makeText(MainActivity.this, "Número de médio kills por partida", Toast.LENGTH_LONG).show();
            }
        });

        rD.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Toast.makeText(MainActivity.this, "Número de médio mortes por partida", Toast.LENGTH_LONG).show();
            }
        });

        rV.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Toast.makeText(MainActivity.this, "Porcentagem de vitórias", Toast.LENGTH_LONG).show();
            }
        });

        kd.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Toast.makeText(MainActivity.this, "Kills/Deaths", Toast.LENGTH_LONG).show();
            }
        });
    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {

        MenuInflater inflater = getMenuInflater();
        inflater.inflate(R.menu.main_menu, menu);

        return true;
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {

        switch (item.getItemId()){

            case R.id.action_add:

                Toast.makeText(MainActivity.this, "Adicionar Partida", Toast.LENGTH_LONG).show();

                Intent intent = new Intent(MainActivity.this, NovoJogoActivity.class);
                intent.putExtra("kill", kill);
                intent.putExtra("death", death);
                intent.putExtra("vit", vit);
                intent.putExtra("jogos", jogos);
                intent.putExtra("nome", nomeU);
                startActivity(intent);

                return true;

            case R.id.action_alterar_nome:

                AlertDialog.Builder dialog = new AlertDialog.Builder(MainActivity.this);

                dialog.setTitle("Alterar Nome");
                dialog.setCancelable(false);

                final EditText editText = new EditText(MainActivity.this);
                dialog.setView(editText);

                dialog.setPositiveButton("Alterar", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialogInterface, int i) {

                        nomeU = editText.getText().toString();
                        toolbar.setTitle(nomeU);

                        SharedPreferences sharedPreferences = getSharedPreferences(ARQUIVO_PREFERENCIA, 0);
                        SharedPreferences.Editor editor = sharedPreferences.edit();

                        editor.putString("nome", nomeU);
                        editor.commit();

                        Toast.makeText(MainActivity.this, "Nome Alterado", Toast.LENGTH_LONG).show();
                    }
                });

                dialog.setNegativeButton("Cancelar", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialogInterface, int i) {

                    }
                });

                dialog.create();
                dialog.show();

                return true;

            case R.id.action_reset:

                AlertDialog.Builder dialogR = new AlertDialog.Builder(MainActivity.this);

                dialogR.setTitle("Resetar todos os dados?");
                dialogR.setCancelable(false);

                dialogR.setPositiveButton("Resetar", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialogInterface, int i) {

                        SharedPreferences sharedPreferences = getSharedPreferences(ARQUIVO_PREFERENCIA, 0);
                        SharedPreferences.Editor editor = sharedPreferences.edit();

                        editor.putFloat("kill", 0);
                        editor.putFloat("death", 0);
                        editor.putInt("vic", 0);
                        editor.putInt("jogos", 0);
                        editor.putString("nome", "Insira seu nome");
                        editor.commit();

                        toolbar.setTitle("Insira seu nome");
                        nK.setText("0");
                        nD.setText("0");
                        nV.setText("0");
                        rK.setText("0");
                        rD.setText("0");
                        rV.setText("0");
                        kd.setText("0");

                        extra=null;

                        Toast.makeText(MainActivity.this, "Todos os dados resetados", Toast.LENGTH_LONG).show();
                    }
                });

                dialogR.setNegativeButton("Cancelar", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialogInterface, int i) {

                    }
                });

                dialogR.create();
                dialogR.show();

                return true;

            case R.id.action_sair:
                AlertDialog.Builder dialogS = new AlertDialog.Builder(MainActivity.this);

                dialogS.setTitle("Tem certeza que deseja sair?");
                dialogS.setCancelable(false);

                dialogS.setPositiveButton("Sair", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialogInterface, int i) {

                        Toast.makeText(MainActivity.this, "Logout realizado com sucesso", Toast.LENGTH_LONG).show();

                        autenticacao = ConfiguracaoFirebase.getFirebaseAutenticacao();
                        autenticacao.signOut();

                        Intent intent2 = new Intent(MainActivity.this, Login.class);
                        startActivity(intent2);
                    }
                });

                dialogS.setNegativeButton("Cancelar", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialogInterface, int i) {

                    }
                });

                dialogS.create();
                dialogS.show();

                return true;

            default:
                return super.onOptionsItemSelected(item);
        }
    }
}
