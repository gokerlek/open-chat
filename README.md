# Open Chat

Open Chat, afet anında internet erişiminiz olduğu ancak hiçbir şifrenizi hatırlamadığınız durumlarda, önceden belirlediğiniz arkadaşlarınızla düşük veri modunda iletişim kurmanızı sağlayan bir HTML sohbet uygulamasıdır.

## Özellikler

- **Düşük Veri Kullanımı**: İnternet bağlantınızın sınırlı olduğu durumlarda çalışabilir.
- **Şifre Gerektirmez**: Önceden belirlenmiş kullanıcılar arasında iletişim sağlar.
- **Basit ve Hızlı**: HTML tabanlı bir uygulama olduğu için hızlı ve kolay erişim sunar.

## Gereksinimler

- [Supabase](https://supabase.com/) hesabı
- Supabase SQL arayüzü

## Kurulum

1. Supabase hesabınıza giriş yapın ve bir proje oluşturun.
2. Aşağıdaki SQL komutlarını Supabase SQL arayüzünde çalıştırarak gerekli tabloları ve politikaları oluşturun.

```sql
-- pgcrypto uzantısını etkinleştir
CREATE EXTENSION IF NOT EXISTS pgcrypto;

-- Mevcut tabloları ve politikaları temizle (verileri koru)
DROP POLICY IF EXISTS public_insert ON messages;
DROP POLICY IF EXISTS public_select ON messages;
DROP POLICY IF EXISTS public_select_users ON allowed_users;

-- allowed_users tablosu
CREATE TABLE IF NOT EXISTS allowed_users (
id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
name TEXT UNIQUE NOT NULL
);

-- Örnek kullanıcıları ekle
INSERT INTO allowed_users (name) VALUES
('ahmet'), ('mehmet'), ('dinlemem'), ('bende'), ('isterem')
ON CONFLICT DO NOTHING;

-- messages tablosu
CREATE TABLE IF NOT EXISTS messages (
id BIGSERIAL PRIMARY KEY,
sender TEXT NOT NULL,
content TEXT NOT NULL,
inserted_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
CONSTRAINT fk_sender CHECK (sender IN (SELECT name FROM allowed_users))
);

-- messages için RLS'yi etkinleştir
ALTER TABLE messages ENABLE ROW LEVEL SECURITY;

-- messages için politikalar
CREATE POLICY "public_insert" ON messages
FOR INSERT WITH CHECK (true);
CREATE POLICY "public_select" ON messages
FOR SELECT USING (true);

-- allowed_users için RLS'yi etkinleştir
ALTER TABLE allowed_users ENABLE ROW LEVEL SECURITY;

-- allowed_users için politika
CREATE POLICY "public_select_users" ON allowed_users
FOR SELECT USING (true);
```

3. Uygulamayı çalıştırmak için gerekli HTML dosyalarını sunucunuza yükleyin.

## Katkıda Bulunma

Katkıda bulunmak isterseniz, lütfen bir pull request gönderin veya bir issue açın.

## Lisans

Bu proje MIT lisansı ile lisanslanmıştır.
